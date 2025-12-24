# SPL-LAND: Settlement Planning and Land Development Simulation
## Project Overview
SPL-LAND is a C++ simulation system for settlement planning and development. The program simulates the strategic construction of facilities (hospitals, schools, factories, parks, etc.) across different types of settlements (villages, cities, metropolises) using various planning policies.
The simulation models real-world urban planning considerations by tracking three key metrics:
- **Life Quality** - Impact on residents' well-being (hospitals, schools, parks)
- **Economy** - Economic development and job creation (factories, banks, markets)
- **Environment** - Environmental sustainability (recycling plants, solar farms, wildlife reserves)
## Why We Encoded It This Way
### 1. Object-Oriented Design Principles
We structured the project using core OOP principles to ensure maintainability, extensibility, and clear separation of concerns:
#### Encapsulation
- Each class has a single, well-defined responsibility
- Private members protect internal state (e.g., `Plan` manages its own facilities and scores)
- Public interfaces provide controlled access to functionality
#### Inheritance and Polymorphism
- **SelectionPolicy** hierarchy enables different planning strategies through a common interface
- **BaseAction** hierarchy allows uniform handling of all simulation commands
- Virtual functions enable runtime polymorphism for different behaviors
#### The Rule of Five
All classes with dynamic memory implement:
- Copy constructor
- Copy assignment operator
- Move constructor (C++11)
- Move assignment operator (C++11)
- Destructor
This ensures proper memory management and prevents memory leaks or double-deletion errors.
### 2. Design Patterns
#### Strategy Pattern (SelectionPolicy)
We implemented the Strategy pattern for facility selection to enable dynamic algorithm switching at runtime:
```cpp
class SelectionPolicy {
    virtual const FacilityType& selectFacility(const vector<FacilityType>& options) = 0;
};
```
**Why this approach?**
- **Open/Closed Principle**: Easy to add new selection strategies without modifying existing code
- **Runtime flexibility**: Plans can change their policy during simulation (`changePlanPolicy` command)
- **Clear separation**: Each strategy encapsulates its own selection logic
- **Testability**: Each strategy can be tested independently
**Implemented Strategies:**
- **NaiveSelection**: Simple round-robin selection (for basic settlements)
- **BalancedSelection**: Selects facilities to balance all three metrics (for diverse development)
- **EconomySelection**: Prioritizes economic growth (for industrial zones)
- **SustainabilitySelection**: Focuses on environmental impact (for green cities)
#### Command Pattern (BaseAction)
All user inputs are encapsulated as command objects:
```cpp
class BaseAction {
    virtual void act(Simulation &simulation) = 0;
};
```
**Why this approach?**
- **Action history**: Every command is logged for audit and debugging
- **Undo/Redo capability**: The backup/restore system leverages this structure
- **Uniform handling**: All commands processed through the same interface
- **Separation of concerns**: Simulation logic separated from command parsing
### 3. Memory Management Strategy
#### Smart Pointers and Manual Management
We use a hybrid approach:
- **Raw pointers** for owned resources with explicit deep copying
- **References** for non-owning relationships
- **Rule of Five** implementation for all classes with dynamic memory
**Why not use smart pointers everywhere?**
- Educational project focusing on understanding memory management fundamentals
- Explicit control over object lifetime and copying behavior
- Demonstrates proper implementation of copy/move semantics
- Prepares for understanding smart pointers by mastering manual management first
#### Deep Copy Semantics
All copy operations perform deep copies to ensure independence:
- `Simulation` copies all settlements, plans, and facilities
- `Plan` copies its selection policy
- Actions are copied for the action log
This prevents shared state issues and makes backup/restore straightforward.
### 4. Modular Architecture
#### Header/Implementation Separation
```
include/   # .h files with class declarations
src/       # .cpp files with implementations
```
**Benefits:**
- Faster compilation (only recompile changed modules)
- Clear interface documentation in headers
- Prevents circular dependencies
- Industry-standard practice
#### Auxiliary Utilities
The `Auxiliary` namespace provides parsing utilities:
```cpp
vector<string> Auxiliary::parseArguments(const string &input);
```
This centralizes string manipulation and keeps the core classes focused on domain logic.
### 5. Configuration-Driven Design
The simulation reads initial state from [config_file.txt](config_file.txt):
**Why configuration files?**
- **Flexibility**: Change initial conditions without recompiling
- **Testing**: Easy to create different test scenarios
- **Separation**: Data separated from code
- **User-friendly**: Non-programmers can modify simulation parameters
### 6. Settlement Type Hierarchy
Settlements are categorized by size and capabilities:
```cpp
enum SettlementType {
    VILLAGE,    // Small, limited construction capacity
    CITY,       // Medium development
    METROPOLIS  // Large-scale urban planning
};
```
Each type has different construction limits per time step, modeling realistic development constraints.
### 7. Facility Categories
Facilities are grouped by their primary impact:
```cpp
enum FacilityCategory {
    LIFE_QUALITY,   // Healthcare, education, recreation
    ECONOMY,        // Industry, commerce, finance
    ENVIRONMENT     // Sustainability, conservation
};
```
This categorization:
- Guides selection policies in choosing appropriate facilities
- Helps balance development across different dimensions
- Models real-world planning considerations
### 8. Step-Based Simulation
Construction happens over time with a step-based system:
- Facilities have construction time (based on cost)
- Each `step` command advances the simulation
- Under-construction facilities progress toward completion
**Why step-based?**
- Models realistic construction timelines
- Allows observation of gradual development
- Enables strategic planning over time
- Demonstrates state management across simulation cycles
### 9. Error Handling
The simulation uses status tracking and error reporting:
- Each action sets a status (COMPLETED/ERROR)
- Error messages explain what went wrong
- Invalid inputs don't crash the simulation
This makes the system robust and user-friendly.
### 10. Backup and Restore System
The backup/restore feature enables:
- Experimentation with different strategies
- Recovery from mistakes
- Comparison of different development paths
Implementation uses deep copying of the entire simulation state, demonstrating proper copy semantics.
## Project Structure
```
SPL-LAND/
├── include/              # Header files (.h)
│   ├── Action.h         # BaseAction and all command classes
│   ├── Auxiliary.h      # String parsing utilities
│   ├── Facility.h       # FacilityType and Facility classes
│   ├── Plan.h           # Plan class
│   ├── SelectionPolicy.h # Policy hierarchy
│   ├── Settlement.h     # Settlement class
│   └── Simulation.h     # Main simulation controller
├── src/                 # Implementation files (.cpp)
│   ├── Action.cpp
│   ├── Auxiliary.cpp
│   ├── Facility.cpp
│   ├── main.cpp         # Entry point
│   ├── Plan.cpp
│   ├── SelectionPolicy.cpp
│   ├── Settlement.cpp
│   └── Simulation.cpp
├── bin/                 # Compiled artifacts
│   ├── *.o              # Object files
│   └── simulation       # Executable
├── .devcontainer/       # Docker development environment
├── .vscode/             # VSCode configuration
├── makefile             # Build system
├── config_file.txt      # Initial simulation configuration
└── README.md            # This file
```
## Key Classes and Responsibilities
### Simulation
The main orchestrator that:
- Manages all settlements, plans, facilities, and actions
- Processes user commands
- Maintains action history
- Handles backup/restore operations
### Settlement
Represents a geographic area with:
- Name and type (VILLAGE/CITY/METROPOLIS)
- Development capacity based on type
### Plan
Manages development for one settlement:
- Tracks life quality, economy, and environment scores
- Maintains facilities (under construction and completed)
- Uses a SelectionPolicy to choose next facility
- Advances construction during simulation steps
### SelectionPolicy
Abstract strategy for choosing which facility to build next:
- Analyzes current scores and available facilities
- Implements different planning philosophies
- Enables dynamic strategy changes
### BaseAction
Abstract command representing user actions:
- Encapsulates action parameters
- Implements execution logic
- Tracks completion status and error messages
- Maintains action log
### FacilityType & Facility
- **FacilityType**: Template defining facility properties (cost, impacts)
- **Facility**: Instance under construction or completed, tracks progress
## Technologies Used
- **Language**: C++11
- **Build System**: GNU Make
- **Compiler**: g++ with strict warning flags
- **Standard Library**: Exclusively (no external dependencies)
- **Development**: VSCode with C++ extensions
- **Optional**: Docker dev container support
## Design Decisions Summary
1. **Strategy Pattern** for selection policies → Enables flexible, runtime-switchable algorithms
2. **Command Pattern** for actions → Provides action history and uniform command handling
3. **Rule of Five** implementation → Ensures proper memory management and copy semantics
4. **Configuration-driven** → Separates data from code for flexibility
5. **Step-based simulation** → Models realistic construction timelines
6. **Deep copy semantics** → Prevents shared state issues
7. **Header/implementation separation** → Improves compilation speed and modularity
8. **Enum-based categorization** → Type-safe facility and settlement classification
9. **No external dependencies** → Simplifies build process and demonstrates C++ fundamentals
10. **Comprehensive error handling** → Robust, user-friendly simulation
## Learning Objectives Demonstrated
This project showcases:
- Advanced C++ OOP concepts (inheritance, polymorphism, virtual functions)
- Memory management (Rule of Five, deep copying)
- Design patterns (Strategy, Command)
- Build systems (Make)
- Code organization and modularity
- File I/O and parsing
- State management in simulations
- Software engineering best practices
## License
Academic project for Systems Programming Laboratory course.
