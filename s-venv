#!/bin/bash

# Simple Virtual Environment Manager
# Manages Python virtual environments in ~/simple-venv/

VENV_DIR="$HOME/simple-venv"

# Create venv directory if it doesn't exist
mkdir -p "$VENV_DIR"

# Color codes for output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m' # No Color

# Help function
show_help() {
    echo "Simple Virtual Environment Manager"
    echo ""
    echo "Usage: ~/s-venv.sh [command] [arguments]"
    echo ""
    echo "Commands:"
    echo "  create <name>              Create a new virtual environment"
    echo "  enter <name>               Activate a virtual environment"
    echo "  reset <name>               Reset a virtual environment (removes all packages)"
    echo "  remove <name>              Delete a virtual environment"
    echo "  copy <source> <target>     Copy virtual environment from source to target"
    echo "  list                       List all virtual environments"
    echo "  help                       Show this help message"
    echo ""
    echo "Virtual environments are stored in: $VENV_DIR"
}

# Create venv
create_venv() {
    local name=$1
    if [ -z "$name" ]; then
        echo -e "${RED}Error: Please provide a name for the virtual environment${NC}"
        return 1
    fi
    
    local venv_path="$VENV_DIR/$name"
    
    if [ -d "$venv_path" ]; then
        echo -e "${YELLOW}Virtual environment '$name' already exists${NC}"
        return 1
    fi
    
    echo "Creating virtual environment '$name'..."
    python3 -m venv "$venv_path"
    
    if [ $? -eq 0 ]; then
        echo -e "${GREEN}Successfully created virtual environment '$name'${NC}"
        echo "To activate: ~/s-venv.sh enter $name"
    else
        echo -e "${RED}Failed to create virtual environment${NC}"
        return 1
    fi
}

# Enter venv
enter_venv() {
    local name=$1
    if [ -z "$name" ]; then
        echo -e "${RED}Error: Please provide the name of the virtual environment${NC}"
        return 1
    fi
    
    local venv_path="$VENV_DIR/$name"
    
    if [ ! -d "$venv_path" ]; then
        echo -e "${RED}Error: Virtual environment '$name' does not exist${NC}"
        echo "Available environments:"
        list_venv
        return 1
    fi
    
    local activate_script="$venv_path/bin/activate"
    if [ ! -f "$activate_script" ]; then
        echo -e "${RED}Error: Activation script not found${NC}"
        return 1
    fi
    
    echo -e "${GREEN}Entering virtual environment '$name'${NC}"
    echo "To activate, run:"
    echo "  source $activate_script"
    echo ""
    echo "Or start a new shell with the environment activated:"
    bash --rcfile <(echo ". $HOME/.bashrc; source $activate_script; echo -e '${GREEN}Virtual environment \"$name\" activated${NC}'")
}

# Reset venv
reset_venv() {
    local name=$1
    if [ -z "$name" ]; then
        echo -e "${RED}Error: Please provide the name of the virtual environment${NC}"
        return 1
    fi
    
    local venv_path="$VENV_DIR/$name"
    
    if [ ! -d "$venv_path" ]; then
        echo -e "${RED}Error: Virtual environment '$name' does not exist${NC}"
        return 1
    fi
    
    echo -e "${YELLOW}WARNING: This will reset virtual environment '$name'${NC}"
    echo "All installed packages will be removed."
    read -p "Are you sure? (yes/no): " confirm
    
    if [ "$confirm" != "yes" ]; then
        echo "Reset cancelled"
        return 0
    fi
    
    echo "Resetting virtual environment '$name'..."
    rm -rf "$venv_path"
    python3 -m venv "$venv_path"
    
    if [ $? -eq 0 ]; then
        echo -e "${GREEN}Successfully reset virtual environment '$name'${NC}"
    else
        echo -e "${RED}Failed to reset virtual environment${NC}"
        return 1
    fi
}

# Remove venv
remove_venv() {
    local name=$1
    if [ -z "$name" ]; then
        echo -e "${RED}Error: Please provide the name of the virtual environment${NC}"
        return 1
    fi
    
    local venv_path="$VENV_DIR/$name"
    
    if [ ! -d "$venv_path" ]; then
        echo -e "${RED}Error: Virtual environment '$name' does not exist${NC}"
        return 1
    fi
    
    echo -e "${YELLOW}WARNING: This will permanently delete virtual environment '$name'${NC}"
    read -p "Are you sure? (yes/no): " confirm
    
    if [ "$confirm" != "yes" ]; then
        echo "Deletion cancelled"
        return 0
    fi
    
    echo "Removing virtual environment '$name'..."
    rm -rf "$venv_path"
    
    if [ $? -eq 0 ]; then
        echo -e "${GREEN}Successfully removed virtual environment '$name'${NC}"
    else
        echo -e "${RED}Failed to remove virtual environment${NC}"
        return 1
    fi
}

# Copy venv
copy_venv() {
    local source=$1
    local target=$2
    
    if [ -z "$source" ] || [ -z "$target" ]; then
        echo -e "${RED}Error: Please provide both source and target names${NC}"
        return 1
    fi
    
    local source_path="$VENV_DIR/$source"
    local target_path="$VENV_DIR/$target"
    
    if [ ! -d "$source_path" ]; then
        echo -e "${RED}Error: Source virtual environment '$source' does not exist${NC}"
        return 1
    fi
    
    if [ -d "$target_path" ]; then
        echo -e "${YELLOW}WARNING: Target virtual environment '$target' already exists${NC}"
        echo "This will overwrite the existing environment."
        read -p "Continue? (yes/no): " confirm
        
        if [ "$confirm" != "yes" ]; then
            echo "Copy cancelled"
            return 0
        fi
        
        rm -rf "$target_path"
    fi
    
    echo "Copying virtual environment from '$source' to '$target'..."
    
    # Create new venv
    python3 -m venv "$target_path"
    
    # Copy installed packages
    if [ -f "$source_path/bin/pip" ]; then
        echo "Copying installed packages..."
        "$source_path/bin/pip" freeze > /tmp/requirements_temp.txt
        "$target_path/bin/pip" install -r /tmp/requirements_temp.txt -q
        rm /tmp/requirements_temp.txt
    fi
    
    if [ $? -eq 0 ]; then
        echo -e "${GREEN}Successfully copied virtual environment to '$target'${NC}"
    else
        echo -e "${RED}Failed to copy virtual environment${NC}"
        return 1
    fi
}

# List venvs
list_venv() {
    echo "Available virtual environments in $VENV_DIR:"
    echo ""
    
    if [ ! -d "$VENV_DIR" ] || [ -z "$(ls -A $VENV_DIR 2>/dev/null)" ]; then
        echo "  (no virtual environments found)"
        return 0
    fi
    
    for dir in "$VENV_DIR"/*; do
        if [ -d "$dir" ]; then
            local name=$(basename "$dir")
            local python_version=""
            if [ -f "$dir/bin/python" ]; then
                python_version=$("$dir/bin/python" --version 2>&1 | cut -d' ' -f2)
            fi
            echo -e "  - $name ${GREEN}[Python $python_version]${NC}"
        fi
    done
}

# Main command handling
case "$1" in
    create)
        create_venv "$2"
        ;;
    enter)
        enter_venv "$2"
        ;;
    reset)
        reset_venv "$2"
        ;;
    remove)
        remove_venv "$2"
        ;;
    copy)
        copy_venv "$2" "$3"
        ;;
    list)
        list_venv
        ;;
    help|--help|-h|"")
        show_help
        ;;
    *)
        echo -e "${RED}Error: Unknown command '$1'${NC}"
        echo "Use '~/s-venv.sh help' for usage information"
        exit 1
        ;;
esac
