# HTCondor Module

This repository contains the HTCondor Module with a local data storage submodule. The data storage submodule is configured to use local storage rather than being hosted on GitHub (or BitBucket for work).

## Repository Structure

- `htcondor_module/`: Main module code
- `data_storage/`: Submodule containing data (stored locally, to be faster and be able to bypass storage limits
of using the bitbucket server)

## Setup Instructions

### Cloning the Repository

To clone this repository with the default data storage:

```bash
# Clone with submodules
git clone --recurse-submodules https://github.com/<username>/htcondor_module.git
```
```bash
# Or clone normally then initialize submodules
git clone https://github.com/<username>/htcondor_module.git
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

## For Maintaining the repo potentially

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

### Creating a New Set of Data for a project, this must be followed

A new branch of the submodule `data_storage` must be made, inheriting off of the main branch (it is an empty branch purposefully)

To create a new branch that inherits all data from the main branch:

```bash
cd data_storage
git checkout main
git pull
git checkout -b <new_branch_name>
```
### Post Completion of the project:

```bash
cd data_storage
git add .
git commit -m "Your changes on top of main branch"
git push -u origin <new_branch_name>
```

### Commands used for the purpose of setting up the repo:

#### stopping all branches being cloned on main module cloning:
```bash
git config -f .gitmodules submodule.data_storage.branch main
```
This command will set it up so that the only branch of a submodule which will be cloned when cloning and setting up the main branch will be the one listed at the end (in this case, main)

#### creating a git repo which is locally stored:

```bash 
mkdir <repo_name>
cd repo_name
git init --bare
```
This will instantiate the bare repo which can be used for storage locally. 

#### adding local storage repo as submodule:

```bash 
cd <main_repo>
git submodule add <submodule_url> <localised_path_to_repo>
```

This will add the submodule designated locally (in first chevrons) and place it in the repo in location designated in the second set of chevrons. eg. want to add one called 'data' and have it in first tier of project, just do:

```bash
git submodule add file:///<local_path_to>/data data
```

If the submodule is a local one, the main repo will most likely require the command:

```bash
git config protocal.file.allow always
```

This will unlock the ability to do the `file:///<path>` setup specified in other processes.

#### command to make a fake large file for branch testing:

```bash
fsutil file createnew data.csv 104857600
# 104857600 is equivalent to 100Mb in size
```
This can be used to create files for testing purposes of 
various sizes, names and file types also (not limited to shown above)

#### Create a simple bash script to generate CSV data:

An alternative solution of bash commandlines that will make a bash script that makes random values:

```bash
# make the bash script to make a large csv for testing
echo '#!/bin/bash' > generate_csv.sh
echo 'echo "id,name,value,date" > data1.csv' >> generate_csv.sh
echo 'for i in {1..1000000}; do' >> generate_csv.sh
echo '  echo "$i,item$i,$RANDOM,2025-03-18" >> data1.csv' >> generate_csv.sh
echo 'done' >> generate_csv.sh

# Make it executable
chmod +x generate_csv.sh

# Run it to generate the CSV
./generate_csv.sh
```

## How to see remote/localised branches on new clone:
When cloning the branch, a problem arises that the branches might not be seen. This is due to an incorrect git pushing of the submodule local branches.  
to fix this, when pushing a submodule (local one) make sure to do:
```bash 
git push origin --all
```
instead of just 
```bash
 git push 
 ```

 In addition, on the cloned branch, need to do:
 ```bash
git fetch origin --prune
git branch -a
 ```
 to be able to see all branches properly. 


## Auto-setup the git push origin --all command
to be able to make it so the issue of needing users to run `git push origin --all` isnt a thing, in the local submodule, run these commands:
```bash
git config push.default matching
git config remote.pushdefault origin
git config push default all
```
Now when the command `git push` is run with that submodule, it will actually run `git push origin --all`.

## Auto-setup of git fetch and git branch
to be able to make it so that the `fetch origin --prune` and `git branch -a` are baked into the commands, run:
```bash
git config fetch.prune true
git config alias.branches 'branch -a'
```
This will mean users can just run `git fetch` and `git branches` to complete the above commands. 