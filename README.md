# Basic ELK Stack Implementation in Kali Linux

This project involves the setup and deployment of the ELK (Elasticsearch, Logstash, Kibana) stack on Kali Linux for centralized logging and monitoring in a cybersecurity environment. It uses Elasticsearch to store and search logs, Logstash to collect and parse them, and Kibana to visualize the data.

Don't forget to explore above files, they are there to make the implementation easier.

## Technologies Used:
- **Elasticsearch**
- **Logstash**
- **Kibana**
- **Kali Linux**

## Prerequisites:
- Kali Linux or any Debian-based system (Ubuntu, etc.)
- Java 11 or higher (Elasticsearch requires Java)

## Setup Guide:

### 1. Install Java:
Update your package lists and install OpenJDK 11:
```bash
sudo apt update
sudo apt install openjdk-11-jdk
```
Verify the installation:
```bash
java -version
```

### 2. Install Elasticsearch:
Download and install Elasticsearch:
```bash
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.10.0-amd64.deb
sudo dpkg -i elasticsearch-7.10.0-amd64.deb
```
Enable and start Elasticsearch:
```bash
sudo systemctl enable elasticsearch
sudo systemctl start elasticsearch
```
Check if Elasticsearch is running:
```bash
curl -X GET "localhost:9200/"
```

### 3. Install Logstash:
Download and install Logstash:
```bash
wget https://artifacts.elastic.co/downloads/logstash/logstash-7.10.0-amd64.deb
sudo dpkg -i logstash-7.10.0-amd64.deb
```
Enable and start Logstash:
```bash
sudo systemctl enable logstash
sudo systemctl start logstash
```

### 4. Install Kibana:
Download and install Kibana:
```bash
wget https://artifacts.elastic.co/downloads/kibana/kibana-7.10.0-amd64.deb
sudo dpkg -i kibana-7.10.0-amd64.deb
```
Enable and start Kibana:
```bash
sudo systemctl enable kibana
sudo systemctl start kibana
```
Access Kibana in your browser at `http://localhost:5601`.

### 5. Configure Logstash:
Create a configuration file for Logstash:
```bash
sudo nano /etc/logstash/conf.d/logstash.conf
```
Add the following configuration:
```plaintext
input {
    stdin {}
}

filter {
    grok {
        match => { "message" => "%{COMBINEDAPACHELOG}" }
    }
}

output {
    elasticsearch {
        hosts => ["localhost:9200"]
        index => "logstash-%{+YYYY.MM.dd}"
    }
    stdout { codec => rubydebug }
}
```

### 6. Test ELK Stack:
Run Logstash:
```bash
sudo logstash -f /etc/logstash/conf.d/logstash.conf
```
Send a test log:
```bash
echo "127.0.0.1 - - [22/Oct/2024:12:00:00 +0000] \"GET / HTTP/1.1\" 200 1234" | sudo logstash -f /etc/logstash/conf.d/logstash.conf
```
Open Kibana, go to **Discover**, and check if the log has been indexed.

---

### 7. Visualizing Data in Kibana:

Now that your logs are being processed by Logstash and stored in Elasticsearch, you can create visualizations using Kibana.

#### Step 1: Open Kibana

1. Open your web browser and go to `http://localhost:5601` to access the Kibana dashboard.
2. Once Kibana is loaded, go to the **Discover** tab on the left sidebar.

#### Step 2: Create an Index Pattern

1. Click on **"Stack Management"** in the left menu, then select **"Index Patterns"** under **Kibana**.
2. Click **"Create index pattern"**.
3. In the Index pattern field, enter `logstash-*` and click **"Next step"**.
4. Select `@timestamp` as the time field, then click **"Create index pattern"**.

#### Step 3: Explore Logs in Discover

1. Go back to the **Discover** tab.
2. Select your newly created index pattern `logstash-*` from the top left dropdown.
3. You should see the logs coming from Logstash, which are being stored in Elasticsearch.

#### Step 4: Create Visualizations

1. In the left sidebar, navigate to **"Visualize Library"**.
2. Click **"Create new visualization"**.
3. Choose the type of visualization you want, such as a **Bar chart**, **Pie chart**, **Line chart**, etc.
4. In the **Data panel**, select the index pattern `logstash-*` and choose the fields you want to visualize.
   - Example: To create a bar chart of request counts by status codes, choose the field `http.response.status_code` and split it by terms.
5. Configure the visualization to your liking and click **"Save"**.

#### Step 5: Build Dashboards

1. In the left menu, click **"Dashboard"**.
2. Click **"Create new dashboard"**.
3. Add your saved visualizations to the dashboard.
4. Customize the layout and view real-time data from your logs.

Now, you have a fully functioning ELK stack with visualizations showing insights from your log data!

---

## Troubleshooting

- If you encounter issues with Kibana, try checking the status of your services with:
  ```bash
  sudo systemctl status elasticsearch
  sudo systemctl status logstash
  sudo systemctl status kibana
  ```

- Check Elasticsearch logs for errors:
  ```bash
  sudo journalctl -u elasticsearch
  ```

- Restart the services if needed:
  ```bash
  sudo systemctl restart elasticsearch
  sudo systemctl restart logstash
  sudo systemctl restart kibana
  ```

---

## Next Steps

- Explore **Machine Learning** features in Kibana for anomaly detection.
- Automate log collection from various sources using **Filebeat**.

---
