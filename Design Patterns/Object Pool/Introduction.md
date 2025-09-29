**Object pool** design pattern. This is also a factory pattern and is implemented through a factory method. But the biggest difference between the factory method and the object pool is that factory method always creates a new instance. But the object pool may return a cached instance. Object pool is not an official design pattern. However, it is still very useful in different kinds of applications, especially games. 

If your application is constructing and destroying lots of objects repetitively and this causes performance problems in the application. The problems may be due to the fact that creating the instance of the clauses from the scratch is expensive or repetitive construction and destruction is causing the heap to fragment. So to avoid these issues, an application can store the instances of the clause inside a pool. When it needs an instance, it will request the object from the pool and use it once it is done with that instance, it will be returned to the pool and can be reused later. 

[Code](https://github.com/shash-786/Learning/tree/main/design-patterns/Object%20Pool)
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


// main.cc
std::vector< std::pair< std::string, std::shared_ptr<GameObject> > > objects; 

void Fire() {
    objects.push_back({"missile", GameObjectPool::AcquireGameObject("missile")});
    objects.push_back({"alien", GameObjectPool::AcquireGameObject("alien")});
}

void Animate() {
    for (auto& object: objects) {
        object.second->Update();
    }
}

void Explode() {
    std::cout << " X "; 
    for (auto& object: objects) {
        GameObjectPool::ReleaseGameObject(object.first, object.second);
    }
    objects.clear();
    std::cout << "\n\n" ;
}

void GameLoop() {
    int counter{}, rotations{};
    while(true) {
        ++counter ;
        if(counter == 1) {
            Fire();
           // std::cout << objects.size() << std::endl;
        }

        if(counter >=1 && counter <= 5) {
            Animate();
        }

        if(counter > 5) {
            Explode();
            counter = 0;
            rotations++;

            if (rotations == 3) {
                return;
            }
        }
    }
}

int main(int argc, char const *argv[]) {
    GameLoop();
    return 0;
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