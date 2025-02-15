#!/bin/bash

# Welcome screen
echo "========================================"
echo "       Welcome to the App Setup!        "
echo "========================================"
echo ""
echo "This script will gather device information, create a directory, and process your appKey."
echo ""

# Simulate a short delay for the welcome screen
sleep 2

# Check if the OS is Ubuntu or Debian
if [ -f /etc/os-release ]; then
  os_name=$(grep "^ID=" /etc/os-release | awk -F= '{print $2}' | tr -d '"')
  if [[ "$os_name" != "ubuntu" && "$os_name" != "debian" ]]; then
    echo "Error: This script only supports Ubuntu and Debian."
    exit 1
  fi
else
  echo "Error: Unable to detect the operating system. This script only supports Ubuntu and Debian."
  exit 1
fi

echo "Operating System: $os_name"
echo ""

# Function to check and install required tools
install_if_missing() {
  local tool=$1
  local package=$2
  if ! command -v "$tool" &> /dev/null; then
    echo "The required tool '$tool' is not installed. Installing '$package'..."
    sudo apt-get update -y
    sudo apt-get install -y "$package"
    if ! command -v "$tool" &> /dev/null; then
      echo "Error: Failed to install '$package'. Please install it manually."
      exit 1
    fi
    echo "'$package' installed successfully."
  else
    echo "'$tool' is already installed."
  fi
}

# Check and install required tools
install_if_missing "lscpu" "util-linux"
install_if_missing "dmidecode" "dmidecode"
install_if_missing "lsblk" "util-linux"

# Get the size of the drive partition where the script is being run
partition_size=$(df -h . | awk 'NR==2 {print $2}')
echo "Partition Size: $partition_size"

# Get the total amount of RAM
if command -v free &> /dev/null; then
  total_ram=$(free -h | awk '/^Mem:/ {print $2}')
else
  total_ram="Unknown (requires 'free' command)"
fi
echo "Total RAM: $total_ram"

# Get the CPU model
cpu_model=$(lscpu | grep "Model name" | awk -F: '{print $2}' | sed 's/^[ \t]*//')
echo "CPU Model: $cpu_model"

# Get the number of CPU threads
cpu_threads=$(lscpu | grep "^CPU(s):" | awk -F: '{print $2}' | sed 's/^[ \t]*//')
echo "CPU Threads: $cpu_threads"

# Get the CPU clock speed
cpu_clock_speed=$(lscpu | grep "CPU MHz" | awk -F: '{print $2}' | sed 's/^[ \t]*//')
echo "CPU Clock Speed: $cpu_clock_speed MHz"

# Get the RAM speed and type
ram_speed=$(sudo dmidecode --type memory | grep "Speed:" | grep -v "Unknown" | head -n 1 | awk -F: '{print $2}' | sed 's/^[ \t]*//')
ram_type=$(sudo dmidecode --type memory | grep "Type:" | grep -v "Unknown" | head -n 1 | awk -F: '{print $2}' | sed 's/^[ \t]*//')
echo "RAM Speed: $ram_speed"
echo "RAM Type: $ram_type"

# Get the storage type
storage_type=$(lsblk -d -o name,rota | grep -E '^[a-z]' | awk '{if ($2 == 0) print "SSD"; else print "HDD"}' | head -n 1)
if [[ $(lsblk -d -o name,rota,tran | grep -E '^[a-z]' | grep "nvme") ]]; then
  storage_type="NVMe SSD"
fi
echo "Storage Type: $storage_type"

echo ""

# Create the directory if it doesn't already exist
if [ ! -d "./cogs" ]; then
  echo "Creating the 'cogs' directory..."
  mkdir ./cogs
  echo "'cogs' directory created successfully!"
else
  echo "'cogs' directory already exists. Skipping creation."
fi

# Check if the first argument is provided
if [ -z "$1" ]; then
  echo "Error: No appKey provided. Please pass the appKey as the first argument."
  exit 1
fi

# Assign the first argument to appKey
appKey="$1"

# Display a loading indicator
echo ""
echo "Processing your appKey..."
echo ""

# Loading spinner function
spinner() {
  local pid=$!
  local delay=0.1
  local spinstr='|/-\'
  while [ "$(ps a | awk '{print $1}' | grep $pid)" ]; do
    local temp=${spinstr#?}
    printf " [%c]  " "$spinstr"
    local spinstr=$temp${spinstr%"$temp"}
    sleep $delay
    printf "\b\b\b\b\b\b"
  done
  printf "    \b\b\b\b"
}

# Simulate a process (e.g., validating the appKey)
(sleep 3) & spinner

# Echo the appKey
echo "appKey: $appKey"
echo ""
echo "Setup complete! Thank you for using this script."
