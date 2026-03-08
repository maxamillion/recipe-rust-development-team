# Example Session: LRU Cache Implementation

This document shows an abbreviated example of what a Goose session looks like using the Rust Development Team recipe.

## Command

```bash
goose run rust-development-team.yaml \
  --param task="Implement a thread-safe LRU cache with generic key-value types" \
  --param requirements="Use std library only, support TTL expiration, include comprehensive tests"
```

## Session Output

---

### PHASE 1: ARCHITECTURE & PLANNING - Lead Architect

**Analyzing requirements for thread-safe LRU cache...**

After researching the requirements, here's the architectural plan:

#### System Design

**Core Components:**
1. `LruCache<K, V>` - Main cache structure
2. `CacheEntry<V>` - Wrapper for values with metadata
3. `CacheConfig` - Configuration parameters

**Key Design Decisions:**

1. **Concurrency Strategy**: Use `RwLock` for the cache structure
   - Allows multiple readers, single writer
   - Better performance than `Mutex` for read-heavy workloads

2. **Internal Data Structure**:
   - `HashMap<K, Box<Node<K, V>>>` for O(1) lookups
   - Doubly-linked list for LRU ordering
   - Custom linked list to maintain ownership

3. **TTL Implementation**:
   - Store `Instant` with each entry
   - Lazy expiration check on access
   - Optional background cleanup thread

**Module Organization:**
```
src/
├── lib.rs           # Public API exports
├── cache.rs         # LruCache implementation
├── node.rs          # Internal linked list node
├── config.rs        # Configuration types
└── tests.rs         # Integration tests
```

**Error Handling:**
```rust
pub enum CacheError {
    Expired,
    NotFound,
    LockPoisoned,
}
```

**Public API:**
```rust
impl<K, V> LruCache<K, V> {
    pub fn new(capacity: usize) -> Self;
    pub fn with_ttl(capacity: usize, ttl: Duration) -> Self;
    pub fn get(&self, key: &K) -> Result<V, CacheError>;
    pub fn put(&self, key: K, value: V) -> Result<(), CacheError>;
    pub fn remove(&self, key: &K) -> Result<V, CacheError>;
    pub fn clear(&self);
    pub fn len(&self) -> usize;
}
```

**Testing Strategy:**
- Unit tests for each component
- Integration tests for cache operations
- Concurrent access tests with multiple threads
- TTL expiration tests with time mocking
- Edge cases: zero capacity, single item, overflow

---

### PHASE 2: IMPLEMENTATION - Rust Developer

**Implementing the architectural plan...**

Creating the cache implementation following the plan:

**File: src/lib.rs**
```rust
pub mod cache;
pub mod config;
mod node;

pub use cache::LruCache;
pub use config::CacheConfig;

#[derive(Debug, PartialEq)]
pub enum CacheError {
    Expired,
    NotFound,
    LockPoisoned,
}

impl std::fmt::Display for CacheError {
    fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::fmt::Result {
        match self {
            CacheError::Expired => write!(f, "Cache entry has expired"),
            CacheError::NotFound => write!(f, "Key not found in cache"),
            CacheError::LockPoisoned => write!(f, "Cache lock was poisoned"),
        }
    }
}

impl std::error::Error for CacheError {}
```

**File: src/cache.rs**
```rust
use std::collections::HashMap;
use std::hash::Hash;
use std::sync::RwLock;
use std::time::{Duration, Instant};

use crate::{CacheError, CacheConfig};
use crate::node::{Node, NodePtr};

pub struct LruCache<K, V> {
    map: RwLock<HashMap<K, NodePtr<K, V>>>,
    head: RwLock<Option<NodePtr<K, V>>>,
    tail: RwLock<Option<NodePtr<K, V>>>,
    capacity: usize,
    ttl: Option<Duration>,
}

impl<K: Clone + Eq + Hash, V: Clone> LruCache<K, V> {
    // Implementation details...
    // [Full implementation would be here]
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_basic_put_get() {
        let cache = LruCache::new(2);
        cache.put("key1", "value1").unwrap();
        assert_eq!(cache.get(&"key1").unwrap(), "value1");
    }

    #[test]
    fn test_lru_eviction() {
        let cache = LruCache::new(2);
        cache.put("key1", "value1").unwrap();
        cache.put("key2", "value2").unwrap();
        cache.put("key3", "value3").unwrap();
        assert!(cache.get(&"key1").is_err());
    }
}
```

*[Additional files and full implementation details omitted for brevity]*

**Build results:**
```
✓ Code compiles without warnings
✓ 12 tests passing
✓ No unsafe code used
```

---

### PHASE 3: QUALITY ASSURANCE - Quality Engineer

**Performing comprehensive code review...**

#### Critical Issues

1. **Thread Safety Concern** (src/cache.rs:45)
   - The linked list manipulation is not atomic with the HashMap operations
   - Could lead to inconsistent state under concurrent access
   - **Fix**: Wrap the entire internal state in a single RwLock instead of separate locks

2. **Memory Leak Potential** (src/node.rs:23)
   - Raw pointers in doubly-linked list could create reference cycles
   - **Fix**: Use `Weak` pointers for backward links or reconsider the design

#### Important Issues

3. **Missing Test Coverage**
   - No concurrent access tests
   - TTL expiration edge cases not covered
   - **Add**: Multi-threaded test using `std::thread::spawn`

4. **API Ergonomics** (src/cache.rs:89)
   - `get` returns `Result<V, CacheError>` but requires cloning
   - **Consider**: Returning `Option<V>` for simpler API or using reference counting

5. **Documentation** (src/lib.rs)
   - Missing doc comments on public API
   - No usage examples
   - **Add**: Rustdoc comments with examples

#### Suggested Improvements

6. **Performance**: Consider using `parking_lot::RwLock` for better performance
7. **Ergonomics**: Add `get_or_insert` and `get_or_insert_with` methods
8. **Observability**: Add metrics/statistics (hit rate, evictions, etc.)

**STATUS: REQUEST CHANGES**

Please address Critical and Important issues before approval.

---

### PHASE 4: REFINEMENT - Rust Developer

**Addressing Quality Engineer feedback...**

#### Changes Made

**1. Fixed Thread Safety (Critical #1)**
```rust
pub struct LruCache<K, V> {
    inner: RwLock<CacheInner<K, V>>,
    capacity: usize,
    ttl: Option<Duration>,
}

struct CacheInner<K, V> {
    map: HashMap<K, NodePtr<K, V>>,
    head: Option<NodePtr<K, V>>,
    tail: Option<NodePtr<K, V>>,
}
```
All operations now acquire a single lock, ensuring atomic state updates.

**2. Fixed Memory Safety (Critical #2)**
Used `Rc` and `Weak` pointers properly:
```rust
type NodePtr<K, V> = Rc<RefCell<Node<K, V>>>;
type WeakNodePtr<K, V> = Weak<RefCell<Node<K, V>>>;
```

**3. Added Concurrent Tests (Important #3)**
```rust
#[test]
fn test_concurrent_access() {
    let cache = Arc::new(LruCache::new(100));
    let mut handles = vec![];

    for i in 0..10 {
        let cache_clone = Arc::clone(&cache);
        handles.push(thread::spawn(move || {
            for j in 0..100 {
                cache_clone.put(i * 100 + j, j).unwrap();
                let _ = cache_clone.get(&(i * 100 + j));
            }
        }));
    }

    for handle in handles {
        handle.join().unwrap();
    }
}
```

**4. Improved API (Important #4)**
Changed signature to be more ergonomic while maintaining safety.

**5. Added Documentation (Important #5)**
```rust
/// A thread-safe LRU cache with optional TTL support.
///
/// # Examples
///
/// ```
/// use lru_cache::LruCache;
///
/// let cache = LruCache::new(100);
/// cache.put("key", "value").unwrap();
/// assert_eq!(cache.get(&"key").unwrap(), "value");
/// ```
pub struct LruCache<K, V> { ... }
```

**Build results:**
```
✓ Code compiles without warnings
✓ 18 tests passing (added 6 new tests)
✓ All critical and important issues addressed
```

---

### PHASE 5: RE-REVIEW - Quality Engineer

**Verifying changes...**

#### Review Results

✅ **Critical Issue #1 (Thread Safety)**: RESOLVED
   - Single lock design properly ensures atomicity
   - No race conditions possible

✅ **Critical Issue #2 (Memory Safety)**: RESOLVED
   - Proper use of Rc/Weak prevents cycles
   - Verified with `cargo miri` (no leaks detected)

✅ **Important Issue #3 (Test Coverage)**: RESOLVED
   - Concurrent tests added and passing
   - TTL edge cases covered

✅ **Important Issue #4 (API)**: RESOLVED
   - API is now ergonomic and idiomatic

✅ **Important Issue #5 (Documentation)**: RESOLVED
   - Comprehensive doc comments with examples
   - `cargo doc` generates good documentation

#### Final Quality Check

- ✅ Code compiles without warnings
- ✅ All tests pass (18/18)
- ✅ Follows Rust idioms and best practices
- ✅ Thread-safe implementation verified
- ✅ Memory-safe (no leaks, no unsafe code)
- ✅ Well documented
- ✅ Comprehensive test coverage
- ✅ Meets all original requirements

**STATUS: APPROVED**

The implementation is production-ready and meets all quality standards.

---

## Summary

**Total Review Cycles**: 1 (out of 10 maximum)

**Deliverables**:
- Thread-safe LRU cache implementation
- TTL expiration support
- 18 comprehensive tests including concurrency tests
- Full documentation with examples
- Zero unsafe code
- No compiler warnings

**Files Created**:
- `src/lib.rs` - Public API and error types
- `src/cache.rs` - Main cache implementation
- `src/node.rs` - Internal linked list nodes
- `src/config.rs` - Configuration types
- `Cargo.toml` - Project manifest

The implementation is ready for use!
