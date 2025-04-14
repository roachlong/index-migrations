# index-migrations
This repository was created to confirm the behavior of index migration scripts with CRDB and Liquibase.  The schema and objects that we'll use were borrowed from the blog post on Medium at [Sequences & Distribution & Performance, Oh My!](https://medium.com/@jleelong_8398/sequences-distribution-performance-oh-my-8449b5ecf878).

If you follow that guide then you can continue with **Execution** after setting up your environment.

# Setup
Below are the steps to setup a local dev environment on MacOS

## Java
```
brew install openjdk@17
sudo ln -sfn /opt/homebrew/opt/openjdk@17/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-17.jdk
java --version
```

## Maven
```
brew install maven
mvn -v
```

## VS Code
```
brew install --cask visual-studio-code
```

## Git
```
brew install git
mkdir -p workspace && cd workspace
git clone git clone https://github.com/roachlong/index-migrations.git
cd index-migrations
```

# Execution
Now open the index-migrations project folder in VS Code and run the IndexMigrationsApplication launch configuration to test the scenario outlined in the changelog.

The only migration script is add-device-ops-idx.xml.  This will run two statements.
1) First to CREATE INDEX device_ops_idx ON public.device_operations (device_id);
2) And then to wait for index creation job to complete

Statement one returns immediately because CRDB schema changes execute asynchronously.  The second statement will use a Liquibase Custom Task Change imlemented in Java to query for the status of the index creation job and loop until the job completes.  Only after the Custom Task Change returns will you see the create-index-and-wait in the databasechangelog, indicating that the next changeset will not start until the index creation job is completed.
