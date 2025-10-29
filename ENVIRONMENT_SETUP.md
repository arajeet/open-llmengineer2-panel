# Environment Variable Setup for Gemini API Key

The plugin now uses the `GEMINI_API_KEY` environment variable instead of hardcoding the API key. This is more secure and follows best practices.

## Get Your API Key

1. Go to [Google AI Studio](https://makersuite.google.com/app/apikey)
2. Sign in with your Google account
3. Click "Create API Key"
4. Copy the generated API key

## Setup Methods

Choose the method that matches your Grafana installation:

---

## Method 1: Systemd Service (Linux - Recommended)

If you're running Grafana as a systemd service:

### Option A: Using systemd override file

```bash
# Create override directory
sudo mkdir -p /etc/systemd/system/grafana-server.service.d

# Create environment file
sudo tee /etc/systemd/system/grafana-server.service.d/gemini.conf << 'EOF'
[Service]
Environment="GEMINI_API_KEY=your_actual_api_key_here"
EOF

# Reload systemd and restart Grafana
sudo systemctl daemon-reload
sudo systemctl restart grafana-server

# Verify the environment variable is set
sudo systemctl show grafana-server | grep GEMINI_API_KEY
```

### Option B: Using environment file

```bash
# Create environment file
sudo nano /etc/default/grafana-server

# Add this line:
GEMINI_API_KEY=your_actual_api_key_here

# Restart Grafana
sudo systemctl restart grafana-server
```

---

## Method 2: Docker / Docker Compose

If you're running Grafana in Docker:

### docker-compose.yml

```yaml
services:
  grafana:
    image: grafana/grafana:latest
    environment:
      - GF_PLUGINS_ALLOW_LOADING_UNSIGNED_PLUGINS=open-llmengineer2-panel
      - GEMINI_API_KEY=your_actual_api_key_here
    volumes:
      - ./dist:/var/lib/grafana/plugins/open-llmengineer2-panel
    ports:
      - "3000:3000"
```

### Using .env file with Docker Compose

```bash
# Create .env file
cat > .env << 'EOF'
GEMINI_API_KEY=your_actual_api_key_here
EOF

# Update docker-compose.yml to use env_file
```

```yaml
services:
  grafana:
    image: grafana/grafana:latest
    env_file:
      - .env
    environment:
      - GF_PLUGINS_ALLOW_LOADING_UNSIGNED_PLUGINS=open-llmengineer2-panel
    volumes:
      - ./dist:/var/lib/grafana/plugins/open-llmengineer2-panel
    ports:
      - "3000:3000"
```

### Docker run command

```bash
docker run -d \
  --name grafana \
  -p 3000:3000 \
  -e GF_PLUGINS_ALLOW_LOADING_UNSIGNED_PLUGINS=open-llmengineer2-panel \
  -e GEMINI_API_KEY=your_actual_api_key_here \
  -v $(pwd)/dist:/var/lib/grafana/plugins/open-llmengineer2-panel \
  grafana/grafana:latest
```

---

## Method 3: Grafana Configuration File

Edit your `grafana.ini`:

```bash
sudo nano /etc/grafana/grafana.ini
```

Add under the `[plugin.open-llmengineer2-panel]` section:

```ini
[plugin.open-llmengineer2-panel]
env_GEMINI_API_KEY = your_actual_api_key_here
```

Restart Grafana:

```bash
sudo systemctl restart grafana-server
```

---

## Method 4: Development/Testing

For local development:

```bash
# Set environment variable temporarily
export GEMINI_API_KEY=your_actual_api_key_here

# Run Grafana with the environment variable
grafana-server
```

Or create a `.env` file in your project root:

```bash
cp .env.example .env
# Edit .env and add your API key
```

---

## Verification

After setting up the environment variable, verify the plugin loads correctly:

### Check Grafana logs:

```bash
# Systemd
sudo journalctl -u grafana-server -f

# Docker
docker logs -f <container-name>
```

### Look for these messages:

- ✅ Success: Plugin should load without errors
- ❌ Error: If you see "GEMINI_API_KEY environment variable not set", the environment variable is not properly configured

### Test the plugin:

1. Open Grafana at http://localhost:3000
2. Create or edit a dashboard
3. Add the "Llm-Engineer-2" panel
4. Click "Analyze Dashboard"
5. You should see the AI analysis (not an API key error)

---

## Security Best Practices

### ✅ DO:
- Use environment variables for API keys
- Keep `.env` files out of version control (already in `.gitignore`)
- Use systemd override files or Docker secrets for production
- Rotate API keys regularly
- Use different API keys for development and production

### ❌ DON'T:
- Hardcode API keys in source code
- Commit `.env` files to Git
- Share API keys in chat, email, or screenshots
- Use production API keys in development
- Store API keys in public repositories

---

## Troubleshooting

### Error: "GEMINI_API_KEY environment variable not set"

**Cause**: The environment variable is not visible to the Grafana process.

**Solutions**:
1. Make sure you ran `systemctl daemon-reload` after creating the override file
2. Verify the environment variable is set: `sudo systemctl show grafana-server | grep GEMINI`
3. Check that you restarted Grafana after setting the variable
4. Ensure there are no typos in the variable name

### Error: "failed to create gemini client"

**Cause**: Invalid API key or network issues.

**Solutions**:
1. Verify your API key is correct
2. Check that your API key has the necessary permissions
3. Ensure your server can reach Google's API endpoints
4. Check API quota limits at https://makersuite.google.com/

---

## Quick Setup Script

For systemd installations, you can use this script:

```bash
#!/bin/bash
# setup-gemini-key.sh

read -sp "Enter your Gemini API Key: " API_KEY
echo

sudo mkdir -p /etc/systemd/system/grafana-server.service.d
sudo tee /etc/systemd/system/grafana-server.service.d/gemini.conf << EOF
[Service]
Environment="GEMINI_API_KEY=${API_KEY}"
EOF

sudo systemctl daemon-reload
sudo systemctl restart grafana-server

echo "✓ Gemini API key configured successfully!"
echo "Check status: sudo systemctl status grafana-server"
```

Save and run:

```bash
chmod +x setup-gemini-key.sh
./setup-gemini-key.sh
```
