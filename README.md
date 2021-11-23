# HashTable
## Simple hash table implementation to show the main idea. Something around std::unordered_multimap.
```cpp
#include <vector>
#include <list>

template<typename K, typename V>
class HashNode {
private:
    K key_;
    V value_;
public:
    explicit HashNode(const K &key, const V &value) : key_(key), value_(value) {}

    [[nodiscard]] K key() const {
        return key_;
    }

    [[nodiscard]] V value() const {
        return value_;
    }
};

template<typename K>
class HashFunc {
public:
    size_t operator()(const K& key, size_t size) {
        return std::hash<K>{}(key) % size;
    }
};

template<typename K, typename V, typename H = HashFunc<K>>
class HashTable {
private:
    constexpr static size_t size_ = 1000;
    std::vector<std::list<HashNode<K, V>>> data_;
    H hashFunc_;
public:
    HashTable() : data_(size_, std::list<HashNode<K, V>>()) {}

    void insert(const K& key, const V& value) {
        data_[hashFunc_(key, size_)].template emplace_back(key, value);
    }

    void remove(const K& key, const V& value) {
        auto index = hashFunc_(key, size_);
        for (auto i = data_[index].begin(); i != data_[index].end(); ++i) {
            if (i->value() == value) {
                data_[index].erase(i);
                return;
            }
        }
    }

    void remove(const K& key) {
        data_[hashFunc_(key, size_)].clear();
    }

    size_t count(const K& key) {
        return data_[hashFunc_(key, size_)].size();
    }

    bool has(const K& key) {
        return count(key) > 0;
    }
};
```
