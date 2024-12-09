To install the New Relic Infrastructure agent specifically on a mounted disk and ensure it consumes storage only from that mount point, you can configure the agent to store its data and logs on the mounted drive. Here’s how you can do that:

### 1. Create and Prepare the Mount Point
Ensure you have already created the mount point and mounted the drive as previously described. If not, you can follow the earlier steps.

Assume that the mount point is `/mnt/mydrive`, where you want New Relic to store its data.

### 2. Install the New Relic Infrastructure Agent
Follow the same steps for installing the New Relic agent, as detailed in the previous response:

```bash
sudo apt update
sudo apt install -y curl gnupg
curl -L https://download.newrelic.com/infrastructure_agent/gpg/newrelic.asc | sudo tee /etc/apt/trusted.gpg.d/newrelic.asc
echo "deb [arch=amd64] https://download.newrelic.com/infrastructure_agent/linux/apt stable main" | sudo tee /etc/apt/sources.list.d/newrelic-infra.list
sudo apt update
sudo apt install newrelic-infra -y
```

### 3. Configure the New Relic Agent to Use the Mount Point for Storage

Now that the agent is installed, you need to configure it to store its data, logs, and other configurations on the mounted disk.

#### Step 1: Set the New Relic Data Directory
You can specify the directory where the New Relic agent stores its data and logs by editing the agent’s configuration.

1. **Create directories for New Relic data and logs on the mounted drive:**

   ```bash
   sudo mkdir -p /mnt/mydrive/newrelic/data
   sudo mkdir -p /mnt/mydrive/newrelic/logs
   ```

2. **Change ownership of these directories** to ensure that the New Relic agent can write to them:

   ```bash
   sudo chown -R newrelic:newrelic /mnt/mydrive/newrelic
   ```

#### Step 2: Modify the New Relic Configuration

The default New Relic configuration files do not provide direct options to specify data directories, but you can override them by setting environment variables for the agent to use custom paths for logs and data.

1. Open the New Relic agent configuration file:

   ```bash
   sudo nano /etc/newrelic-infra.yml
   ```

2. Add or modify the following lines under the `newrelic-infra.yml` configuration to specify custom directories for data and logs:

   ```yaml
   log_file: /mnt/mydrive/newrelic/logs/newrelic-infra.log
   data_directory: /mnt/mydrive/newrelic/data
   ```

   This will make sure that the New Relic agent stores its logs and data in the specified directories on your mounted disk.

#### Step 3: Set Permissions and Restart the Agent

Ensure that the directories you specified have the correct permissions for the New Relic agent to write logs and data:

```bash
sudo chown -R newrelic:newrelic /mnt/mydrive/newrelic
```

Now, restart the New Relic agent to apply the changes:

```bash
sudo systemctl restart newrelic-infra
```

#### Step 4: Verify the Agent is Running and Using the Mount Point

Check if the agent is running and using the specified directories on the mount point:

```bash
sudo systemctl status newrelic-infra
```

Check the New Relic agent logs to confirm that the logs are being written to the correct location:

```bash
cat /mnt/mydrive/newrelic/logs/newrelic-infra.log
```

You should see New Relic logs being generated in the `/mnt/mydrive/newrelic/logs/` directory.

### Summary of Changes

- **Logs and data** for the New Relic agent are stored in `/mnt/mydrive/newrelic/logs/` and `/mnt/mydrive/newrelic/data/`.
- The configuration file (`/etc/newrelic-infra.yml`) was modified to specify these custom paths.
- After making changes, the agent was restarted to use the new configuration.

This ensures that the New Relic agent will consume storage from the mounted disk only, without interfering with the system's root file system.
