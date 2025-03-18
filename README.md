# HTCondor Module

This repository contains the HTCondor Module with a local data storage submodule. The data storage submodule is configured to use local storage rather than being hosted on GitHub.

## Repository Structure

- `htcondor_module/`: Main module code
- `data_storage/`: Submodule containing data (stored locally)

## Setup Instructions

### Cloning the Repository

To clone this repository with the default data storage:

```bash
# Clone with submodules
git clone --recurse-submodules https://github.com/YOUR_USERNAME/htcondor_module.git

# Or clone normally then initialize submodules
git clone https://github.com/YOUR_USERNAME/htcondor_module.git
cd htcondor_module
git submodule init
git submodule update
```

By default, the data storage submodule will track the `main` branch.

### Using a Different Data Branch

If you want to use a different branch of data:

```bash
cd htcondor_module
git submodule init
git -c submodule.data_storage.update=checkout \
    -c submodule.data_storage.branch=<branch_name> \
    submodule update
```

Replace `<branch_name>` with the name of the branch you want to use.

## Working with the Data Storage Submodule

### Making Changes to Data

To add or modify data within the submodule:

```bash
cd data_storage
# Make your changes
git add .
git commit -m "Your commit message"
git push
```

### Creating a New Data Branch

To create a new branch for different data sets:

```bash
cd data_storage
git checkout -b <new_branch_name>
# Add or modify data
git add .
git commit -m "Initial commit for new data branch"
git push -u origin <new_branch_name>
```

## For Maintainers

### Configuring Default Branch

To permanently configure the submodule to track a specific branch:

```bash
git config -f .gitmodules submodule.data_storage.branch main
git submodule update --remote
git add .gitmodules
git commit -m "Configure data_storage submodule to track main branch"
git push
```

### Updating Submodule References

When the submodule has new commits:

```bash
cd htcondor_module
git submodule update --remote
git add data_storage
git commit -m "Update data_storage reference"
git push
```

## Troubleshooting

### Submodule Not Initializing

If you see an empty data_storage directory:

```bash
git submodule init
git submodule update
```

### Permission Issues with Local Repository

For issues accessing the local bare repository:

```bash
git config --global protocol.file.allow always
```

Then try the submodule operations again.