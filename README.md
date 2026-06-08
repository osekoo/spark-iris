# Spark App - Iris Data Classification

This project demonstrates Iris dataset classification with Apache Spark and provides a practical guide to running Spark applications on a cluster.

## Set up the App
- Clone or download this Git [repository](https://github.com/osekoo/spark-iris) to your local machine.  
Open your project with your favorite IDE (IntelliJ, VS Code, etc.)
Then, explore the main application file:
```
src/main/scala/MainApp.scala
```

## Run App Locally
First, package the application
```
sbt package
```

Then, run the application:

For Windows user:
```bash
./run-app.bat
```

For Linux/MacOS
```bash
chmod +x ./run-app.sh
./run-app
```

> The application will run inside docker container.

## Run App on the LAMSADE Cluster

### Step 1 - Set up your SSH Key:
Request your personal SSH key (`your_key`) from the administrator.  
It will look something like:
```
id_123_<your_username>.key
```
If you encounter permission issue (SSH will reject your key if it is too visible to others):  
**On Linux/macOS**
```bash
chmod 600 <your_key>
```
**On Windows**
- Right-click on the key file → Properties → Security tab
- Remove access for `Users` or `Everyone`
- Make sure **only your user** has Full Control

### Step 2 – Upload the Iris Data Folder to Your Cluster Workspace
First, connect to the LAMSADE cluster:

```bash
ssh -p 5022 -i <your_key> <your_username>@ssh.lamsade.dauphine.fr
```

Then, create your workspace on remote machine
```bash
mkdir -p ~/workspace/data
```

Now, from your local machine:
```bash
scp -P 5022 -i <your_key> -r data <your_username>@ssh.lamsade.dauphine.fr:~/workspace
```

### Step 3 – Copy Data to HDFS (on the remote server)
First, connect to the LAMSADE cluster:

```bash
ssh -p 5022 -i <your_key> <your_username>@ssh.lamsade.dauphine.fr
```
Then, upload the data to hdfs:
```bash
cd ~/workspace
hdfs dfs -mkdir -p /students/emiasd7/<your_username>/data
hdfs dfs -put data/* /students/emiasd7/<your_username>/data
```

### Step 4 – Upload the Iris App JAR to Your Workspace

From your local machine, copy the JAR file `target/scala-2.12/iris_2.12-0.1.jar`
```bash
scp -P 5022 -i <your_key> target/scala-2.12/iris_2.12-0.1.jar <your_username>@ssh.lamsade.dauphine.fr:~/workspace
```

### Step 5 – Run the Spark Application
First, connect to the LAMSADE cluster:

```bash
ssh -p 5022 -i <your_key> <your_username>@ssh.lamsade.dauphine.fr
```

Then, run your Spark job from your workspace:
```bash
cd ~/workspace

spark-submit \
    --deploy-mode client \
    --executor-cores 4 \
    --executor-memory 2G \
    --num-executors 1 \
    --class "MainApp" \
    iris_2.12-0.1.jar \
    /students/p6emiasd2025/<your_username>/data/Iris.csv \
    /students/p6emiasd2025/<your_username>/output
```

> `/students/p6emiasd2025/<your_username>/data/Iris.csv` is the HDFS path  
> `/students/p6emiasd2025/<your_username>/output` is the HDFS output folder (will be created if it doesn't exist)


## Notes

- You can monitor the job via the **Spark UI** by opening a tunnel:
```bash
ssh -p 5022 -i <your_key> <your_username>@ssh.lamsade.dauphine.fr -L 8080:vmhadoopmaster.cluster.lamsade.dauphine.fr:8080
```
Then open [http://localhost:8080](http://localhost:8080) in your browser.
