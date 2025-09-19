**Object pool** design pattern. This is also a factory pattern and is implemented through a factory method. But the biggest difference between the factory method and the object pool is that factory method always creates a new instance. But the object pool may return a cached instance. Object pool is not an official design pattern. However, it is still very useful in different kinds of applications, especially games. 

If your application is constructing and destroying lots of objects repetitively and this causes performance problems in the application. The problems may be due to the fact that creating the instance of the clauses from the scratch is expensive or repetitive construction and destruction is causing the heap to fragment. So to avoid these issues, an application can store the instances of the clause inside a pool. When it needs an instance, it will request the object from the pool and use it once it is done with that instance, it will be returned to the pool and can be reused later. 

```cpp
// GameObjectPool.h
#include "GameObject.h"
#include <string>
#include <unordered_map>
#include <vector>
#include <memory>
#include <algorithm>

using GameObjectPtr = std::shared_ptr<GameObject>;

class GameObjectPool {
    private:
    inline static std::unordered_map< std::string, std::vector< GameObjectPtr > > m_pool;
    static GameObjectPtr InternalCreate(const std::string& key);
    GameObjectPool()= default;

    public:
    static GameObjectPtr AcquireGameObject(const std::string& key);
    static void ReleaseGameObject(const std::string& key, const GameObjectPtr& object);
    static void RegisterCreator(const std::string& key, const std::string& creator);
};

// GameObjectPool.cc

GameObjectPtr GameObjectPool::InternalCreate(const std::string& key) {
    if (key == "missile") {
        return std::make_shared<Missile>();
    } else if (key == "alien") {
        return std::make_shared<Alien>();
    } else {
        throw std::runtime_error("undefined gameobject type");
    }
}

GameObjectPtr GameObjectPool::AcquireGameObject(const std::string& key) {
    GameObjectPtr object = nullptr;
    if (m_pool.find(key) == m_pool.end()) {
        object = InternalCreate(key);
        m_pool[key].push_back(object);
        std::cout << "[POOL] Creating a New "<< key << " Object" << std::endl;
        return object;
    }

    auto objects = m_pool[key];
    auto object_ptr = std::find_if(begin(objects), end(objects), [](const auto& OBJECT){
        return !OBJECT->isVisible();
    });

    if (object_ptr != end(objects)) {
        object = *object_ptr;
        object->SetVisibility(true);
        std::cout << "[POOL] Reusing an Existing "<< key << " Object" << std::endl;
        return object;
    }

    object = InternalCreate(key);
    m_pool[key].push_back(object);
    std::cout << "[POOL] Creating a New "<< key << " Object" << std::endl;
    return object;
}

void GameObjectPool::ReleaseGameObject(const std::string& key, const GameObjectPtr& object) {
    if (m_pool.find(key) == m_pool.end()) {
        throw std::runtime_error("undefined gameobject type");
    }

    auto objects = m_pool[key];
    for (auto& obj: objects) {
        if (obj == object) {
            // RESET STATE
            obj->SetVisibility(false);
            return;
        }
    }

    throw std::runtime_error("Object Not found");
}



// OUTPUT
Missile Constructed
[POOL] Creating a New missile Object
Alien Constructed
[POOL] Creating a New alien Object
-> @ -> @ -> @ -> @ -> @  X 

[POOL] Reusing an Existing missile Object
[POOL] Reusing an Existing alien Object
-> @ -> @ -> @ -> @ -> @  X 

[POOL] Reusing an Existing missile Object
[POOL] Reusing an Existing alien Object
-> @ -> @ -> @ -> @ -> @  X 

Alien Destructed
Missile Destructed
```