# ECS — A Minimal, Data-Oriented Entity Component System

A **lightweight, high-performance Entity Component System (ECS)** written in modern C++, designed for **clarity, control, predictability and raw performance**.

This is not a framework.  
It is a foundation.

---

## Philosophy

This ECS is built with a few uncompromising principles:

- **Data-oriented design first**  
  Memory layout and access patterns matter more than abstractions.

- **Zero-cost abstractions**  
  No virtual dispatch, no hidden allocations, no runtime surprises.

- **Explicit over implicit**  
  Systems do exactly what you tell them to do — nothing more.

- **Minimal but complete**  
  Only the core building blocks are provided.

- **Predictable performance**  
  Deterministic iteration and cache-friendly storage.

This project values *understanding* over convenience.

---

## Core Concepts

### Entities
Entities are lightweight identifiers — nothing more than keys.

### Components
Components are plain data structures.  
No inheritance. No behavior. Just state.

### Sparse Sets
Component storage is implemented using a sparse–dense layout:
- O(1) insertion and lookup  
- Tight, cache-friendly iteration  
- Stable performance characteristics  

### Systems
Systems operate explicitly over component sets.  
No reflection, no magic scheduling.

---

## Features

- Sparse-set–based storage  
- Cache-efficient iteration  
- Deterministic behavior  
- No RTTI  
- No dynamic polymorphism  
- Explicit ownership and lifetime control  
- Designed for games and simulations  

---

## Example

```cpp
//#include<your files>
#include "Custom_ECS/include/EntityComponentSystem.hpp" // the header to include the ecs

entity e1 = myecs::create_entity(); // entity is just a typedef for a integer
entity e2 = myecs::create_entity();
entity e3 = myecs::create_entity();
entity e4 = myecs::create_entity();

myecs::add_comp_to<comp::position>(e1); // all of them will have position component
myecs::add_comp_to<comp::position>(e2);
myecs::add_comp_to<comp::position>(e3);
myecs::add_comp_to<comp::position>(e4);

myecs::add_comp_to<comp::physics>(e2);// only e1 and e2 will have physics component (whilst having position component)
myecs::add_comp_to<comp::physics>(e1); 

// where comp::position is just

//file: components.hpp
//
//namespace comp{
//
//    struct position { // STRIICTLY FOLLOWS -STRUCT OF ARRAYS- (SoA) DESIGN PATTERN
//        myecs::d_array<T> x;
//        myecs::d_array<T> y;
//    };
//}



// NOTE : Evetything is properly asserted, no UB misuse is possible in debug builds. 

// suppose we want to access every entity's position x {tight dense arrays (no holes upon deletion)}

int pos_x_size = myecs::storage<comp::position>::pointer->x.size();
int pos_y_size = myecs::storage<comp::position>::pointer->y.size();
// (pos_x_size == pos_y_size) always true 
// for ease of writing, and showing an example here 
auto& pos_x_array = myecs::storage<comp::position>::pointer->x;
auto& pos_y_array = myecs::storage<comp::position>::pointer->y;
// myecs::storage<Component_Type>::pointer->x.size(), where x is just a myecs::d_array() or std::vector
// access
for (int i = 0; i < pos_x_size; i++) {
    std::cout << "element i of data storing position::x" << pos_x_array[i];
    std::cout << "element i of data storing position::y" << pos_y_array[i];
}

// suppose we want to access by entity id (macro provided) 
// ecs_access(Component_Type, entity_id, element_name); // returns T& element_array[index_correponding_entity_id]

ecs_access(comp::position, e1, x) = 100;
ecs_access(comp::position, e1, y) = 100;
ecs_access(comp::position, e2, x) = ecs_access(comp::position, e1, x);
ecs_access(comp::position, e2, y) = ecs_access(comp::position, e1, y);

