# SPL-LAND: Setup and Run Instructions
## Prerequisites
### Required Software
- **C++ Compiler**: g++ with C++11 support
- **Build Tool**: GNU Make
- **Operating System**: Linux, macOS, or Windows with appropriate toolchain
### Optional
- **Docker**: For containerized development environment (not required for building/running)
- **VSCode**: Recommended IDE with C++ extensions
## Installation
### Option 1: Native Environment (Recommended for Quick Start)
1. **Verify g++ installation:**
   ```bash
   g++ --version
   ```
   Should show g++ version 4.8.1 or higher for C++11 support.
2. **Verify make installation:**
   ```bash
   make --version
   ```
3. **Clone/Download the project:**
   ```bash
   cd "c:\Users\User\Desktop\studies\תכנות מערכות\SPL-LAND"
   ```
### Option 2: Docker Development Container (Optional)
If you prefer a containerized environment:
1. **Install Docker Desktop** (if not already installed)
2. **Install VSCode and Remote-Containers extension**
3. **Open in container:**
   - Open VSCode
   - Open the project folder
   - VSCode should prompt to "Reopen in Container"
   - Click "Reopen in Container" or use Command Palette: "Remote-Containers: Reopen in Container"
**Note:** Docker is NOT required for building, running, or committing to Git. It's purely optional for development.
## Building the Project
### Clean Build (Recommended First Time)
```bash
make all
```
This command runs:
1. `make clean` - Removes old compiled files
2. `make compile` - Compiles all source files to object files
3. `make link` - Links object files into the executable
### Individual Build Steps
**Clean only:**
```bash
make clean
```
**Compile only:**
```bash
make compile
```
**Link only:**
```bash
make link
```
### Build Output
After successful build, you'll find:
- Object files in `bin/*.o`
- Executable at `bin/simulation`
## Running the Simulation
### Basic Execution
```bash
bin/simulation config_file.txt
```
This launches the simulation with the default configuration file.
### Using a Custom Configuration File
```bash
bin/simulation path/to/your/config.txt
```
## Configuration File Format
The configuration file defines the initial simulation state. Format:
```
# Lines starting with # are comments
# Add settlements: settlement <name> <type>
# Types: 0=VILLAGE, 1=CITY, 2=METROPOLIS
settlement KfarSPL 0
settlement KiryatSPL 2
settlement BeitSPL 1
# Add facility types: facility <name> <category> <price> <life_quality> <economy> <environment>
# Categories: 0=LIFE_QUALITY, 1=ECONOMY, 2=ENVIRONMENT
facility Hospital 0 5 5 3 2
facility School 0 4 4 2 2
facility Factory 1 5 2 5 1
facility RecyclingPlant 2 5 3 1 5
# Create plans: plan <settlement_name> <policy>
# Policies: nve, bal, eco, sus
plan KfarSPL eco
plan KiryatSPL bal
```
### Configuration Parameters Explained
**Settlement Types:**
- `0` (VILLAGE) - Small settlement, limited construction capacity
- `1` (CITY) - Medium-sized settlement
- `2` (METROPOLIS) - Large city with high construction capacity
**Facility Categories:**
- `0` (LIFE_QUALITY) - Hospitals, schools, parks, community centers
- `1` (ECONOMY) - Factories, markets, banks, warehouses
- `2` (ENVIRONMENT) - Recycling plants, solar farms, wildlife reserves
**Selection Policies:**
- `nve` (Naive) - Simple round-robin selection
- `bal` (Balanced) - Balances all three metrics
- `eco` (Economy) - Prioritizes economic development
- `sus` (Sustainability) - Focuses on environmental impact
## Interactive Commands
Once the simulation is running, you can enter commands:
### Settlement Management
**Add a new settlement:** settlement <name> <type>, example: settlement TelSPL 2
### Plan Management
**Create a development plan:** plan <settlement_name> <policy>, example: plan TelSPL bal
**Change a plan's policy:** changePolicy <plan_id> <new_policy>, example: changePolicy 0 sus
**Check plan status:** planStatus <plan_id>, example: planStatus 0
### Facility Management
**Add a new facility type:** facility <name> <category> <price> <life_quality_impact> <economy_impact> <environment_impact>, example: facility University 0 6 6 4 3
### Simulation Control
**Advance the simulation:** step <number_of_steps>, example: step 5 (advances time by 5 steps, progressing construction of facilities)
**View action history:** log
**Save simulation state:** backup
**Restore previous state:** restore
**Exit simulation:** close
## Example Session
Here's a complete example of running the simulation:
```bash
# Build the project
make all
# Run with default config
bin/simulation config_file.txt
# Interactive commands:
> planStatus 0
Plan ID: 0
Settlement: KfarSPL
...
> step 10
> settlement MyVillage 0
> plan MyVillage nve
> step 5
> changePolicy 0 sus
> backup
> step 10
> restore
> log
> close
```
## Troubleshooting
### Build Errors
**Error: "g++ command not found"**
- Install g++ compiler (Linux: `sudo apt-get install g++`, macOS: install Xcode Command Line Tools)
**Error: "make command not found"**
- Install make (Linux: `sudo apt-get install build-essential`, macOS: included with Xcode)
**Error: Compilation errors about C++11**
- Ensure your g++ version supports C++11 (version 4.8.1+)
- The makefile already includes `-std=c++11` flag
### Runtime Errors
**Error: "Cannot open file config_file.txt"**
- Ensure you're running from the project root directory
- Check that the config file path is correct
- Use absolute paths if necessary
**Error: Invalid command**
- Check command syntax against examples above
- Ensure settlement/plan IDs exist before referencing them
- Verify policy names are exactly: `nve`, `bal`, `eco`, or `sus`
**Error: Settlement already exists**
- Settlement names must be unique
- Check existing settlements before adding new ones
### Performance Issues
If the simulation runs slowly:
- Reduce the number of steps per command
- Simplify your configuration file
- Check that you don't have excessive facilities under construction
## File Locations
- **Source code:** `src/*.cpp`
- **Headers:** `include/*.h`
- **Executable:** `bin/simulation`
- **Object files:** `bin/*.o`
- **Configuration:** `config_file.txt` (or custom location)
## Git Workflow
### Do I need Docker running to commit/push to Git?
**No!** Docker is completely independent from Git operations. You can:
- Create files
- Edit code
- Commit changes
- Push to remote repositories
All without Docker running. Docker is only for the optional development container environment.
### Committing Your Changes
```bash
# Add the new documentation files
git add README.md INSTRUCTIONS.md
# Commit
git commit -m "Add comprehensive documentation"
# Push to remote
git push origin master
```
## Development Workflow
### Recommended Workflow
1. **Make changes** to source code in `src/` or `include/`
2. **Clean build:**
   ```bash
   make all
   ```
3. **Test** with configuration file:
   ```bash
   bin/simulation config_file.txt
   ```
4. **Debug** if needed (use VSCode debugger or gdb)
5. **Repeat** until satisfied
### Debugging
**Using VSCode:**
1. Set breakpoints in source code
2. Press F5 to start debugging
3. Use the configured launch.json settings
**Using gdb (command-line):**
```bash
gdb bin/simulation
(gdb) run config_file.txt
```
## Advanced Usage
### Creating Test Scenarios
Create different config files for testing:
**Quick test** (`quick_test.txt`):
```
settlement Test 0
facility QuickFacility 0 1 1 1 1
plan Test nve
```
Run with:
```bash
bin/simulation quick_test.txt
```
### Batch Commands
You can pipe commands to the simulation:
```bash
echo -e "step 5\nplanStatus 0\nclose" | bin/simulation config_file.txt
```
## Additional Resources
- See [README.md](README.md) for architecture and design decisions
- Check `include/*.h` files for detailed class documentation
- Review `config_file.txt` for configuration examples
## Getting Help
If you encounter issues:
1. Check this instructions file
2. Review error messages carefully
3. Verify your environment setup (g++, make versions)
4. Check that file paths are correct
5. Ensure configuration file syntax is valid
## Summary of Common Commands
```bash
# Build
make all
# Run
bin/simulation config_file.txt
# Clean build artifacts
make clean
# Rebuild everything
make all
# Run with custom config
bin/simulation my_config.txt
```
Happy simulating!
