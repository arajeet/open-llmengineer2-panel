# LLM Engineer 2 - AI-Powered Grafana Dashboard Analyzer

![Grafana](https://img.shields.io/badge/Grafana-10.4%2B-orange)
![Go](https://img.shields.io/badge/Go-1.21%2B-blue)
![React](https://img.shields.io/badge/React-18-blue)
![License](https://img.shields.io/badge/license-Apache%202.0-green)

> An intelligent Grafana panel plugin that uses Google's Gemini AI to automatically analyze and provide insights about your dashboards.

## 🎯 What is LLM Engineer 2?

LLM Engineer 2 is a Grafana panel plugin that brings the power of Large Language Models (LLMs) to your observability dashboards. With a single click, it captures your entire dashboard and uses Google's Gemini AI to provide intelligent analysis, insights, and observations about your metrics, trends, and anomalies.

### ✨ Key Features

- **🤖 AI-Powered Analysis**: Leverages Google Gemini 2.0 to understand and analyze your dashboard metrics
- **📸 Full Dashboard Capture**: Captures the entire dashboard, not just a single panel
- **💡 Intelligent Insights**: Automatically identifies trends, anomalies, and patterns in your data
- **⚡ Real-time Processing**: Get instant AI-generated insights with a single button click
- **🔒 Secure**: Uses environment variables for API keys - no hardcoded secrets
- **🌐 Multi-platform**: Pre-built binaries for Linux, macOS (Intel & Apple Silicon), and Windows

## 🚀 Quick Start

### Prerequisites

- Grafana 10.4.0 or higher
- Google Gemini API key ([Get one here](https://makersuite.google.com/app/apikey))

### Installation

1. **Download the latest release**
   ```bash
   # Download the plugin archive
   wget https://github.com/your-repo/open-llmengineer2-panel/releases/latest/download/open-llmengineer2-panel.tar.gz
   ```

2. **Install the plugin**
   ```bash
   # Extract to Grafana plugins directory
   sudo mkdir -p /var/lib/grafana/plugins/open-llmengineer2-panel
   sudo tar -xzf open-llmengineer2-panel.tar.gz -C /var/lib/grafana/plugins/open-llmengineer2-panel/

   # Set proper permissions
   sudo chown -R grafana:grafana /var/lib/grafana/plugins/open-llmengineer2-panel
   ```

3. **Configure unsigned plugin**
   ```bash
   # Using systemd override (recommended)
   sudo mkdir -p /etc/systemd/system/grafana-server.service.d
   sudo tee /etc/systemd/system/grafana-server.service.d/override.conf << 'EOF'
   [Service]
   Environment="GF_PLUGINS_ALLOW_LOADING_UNSIGNED_PLUGINS=open-llmengineer2-panel"
   EOF
   ```

4. **Set up Gemini API key**
   ```bash
   # Quick setup with the provided script
   ./setup-gemini-key.sh

   # Or manually add to systemd
   sudo tee /etc/systemd/system/grafana-server.service.d/gemini.conf << 'EOF'
   [Service]
   Environment="GEMINI_API_KEY=your_actual_api_key_here"
   EOF
   ```

5. **Restart Grafana**
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl restart grafana-server
   ```

### Usage

1. Open any Grafana dashboard
2. Add a new panel
3. Select **"Llm-Engineer-2"** as the visualization type
4. Click the **"Analyze Dashboard"** button
5. Watch as AI provides insights about your metrics! 🎉

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Grafana Dashboard                         │
│  ┌───────────────────────────────────────────────────────┐  │
│  │         LLM Engineer 2 Panel (React)                  │  │
│  │  ┌─────────────────────────────────────────────────┐  │  │
│  │  │  [Analyze Dashboard Button]                     │  │  │
│  │  └─────────────────────────────────────────────────┘  │  │
│  │                      │                                 │  │
│  │                      ▼                                 │  │
│  │         Captures dashboard screenshot                 │  │
│  │                      │                                 │  │
│  └──────────────────────┼─────────────────────────────────┘  │
└───────────────────────────┼─────────────────────────────────┘
                            │
                            ▼
            ┌───────────────────────────────┐
            │   Backend (Go)                │
            │  - Receives screenshot        │
            │  - Decodes base64 image       │
            │  - Sends to Gemini API        │
            └───────────────┬───────────────┘
                            │
                            ▼
            ┌───────────────────────────────┐
            │   Google Gemini 2.0 API       │
            │  - Analyzes image             │
            │  - Identifies metrics         │
            │  - Detects trends/anomalies   │
            │  - Generates insights         │
            └───────────────┬───────────────┘
                            │
                            ▼
                    Returns AI Analysis
                            │
                            ▼
            ┌───────────────────────────────┐
            │   Display in Panel (Green)    │
            └───────────────────────────────┘
```

## 🛠️ Development

### Frontend Development

```bash
# Install dependencies
npm install

# Development mode with hot reload
npm run dev

# Build for production
npm run build

# Run tests
npm run test

# Lint code
npm run lint
```

### Backend Development

```bash
# Build for current platform
go build -o dist/gpx_open-llmengineer2-panel ./pkg

# Build for all platforms
mage buildAll

# Run tests
go test ./...
```

### Local Testing with Docker

```bash
# Start Grafana with plugin
npm run server

# Or with docker-compose
docker compose up
```

## 📋 Configuration

### Environment Variables

- `GEMINI_API_KEY` - **Required** - Your Google Gemini API key
- `GF_PLUGINS_ALLOW_LOADING_UNSIGNED_PLUGINS` - Set to `open-llmengineer2-panel`

See [ENVIRONMENT_SETUP.md](ENVIRONMENT_SETUP.md) for detailed configuration instructions.

## 📚 Documentation

- **[INSTALLATION.md](INSTALLATION.md)** - Complete installation guide
- **[ENVIRONMENT_SETUP.md](ENVIRONMENT_SETUP.md)** - API key and environment configuration
- **[.env.example](.env.example)** - Environment variable template

## 🔒 Security

- ✅ API keys stored as environment variables
- ✅ No hardcoded secrets in source code
- ✅ `.env` files excluded from Git
- ✅ Secure systemd service configuration
- ✅ Backend validates all inputs

**Important**: Never commit API keys to version control. Use environment variables or secrets management.

## 🌟 Features in Detail

### Dashboard Analysis

The plugin provides comprehensive dashboard analysis including:

- **Metric Identification**: Recognizes and describes what metrics are being displayed
- **Trend Analysis**: Identifies upward/downward trends in time-series data
- **Anomaly Detection**: Highlights unusual patterns or spikes in your data
- **Correlation Insights**: Suggests relationships between different metrics
- **Context Understanding**: Interprets the overall health and status shown in the dashboard

### Supported Visualizations

The AI can analyze various Grafana panel types:
- Time-series graphs
- Bar charts
- Stat panels
- Gauge panels
- Tables
- Logs panels
- And more!

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

### Development Setup

1. Fork the repository
2. Clone your fork: `git clone https://github.com/your-username/open-llmengineer2-panel.git`
3. Create a feature branch: `git checkout -b feature/amazing-feature`
4. Make your changes
5. Run tests: `npm test && go test ./...`
6. Commit your changes: `git commit -m 'Add amazing feature'`
7. Push to branch: `git push origin feature/amazing-feature`
8. Open a Pull Request

## 📦 Building from Source

```bash
# Clone the repository
git clone https://github.com/your-username/open-llmengineer2-panel.git
cd open-llmengineer2-panel

# Install frontend dependencies
npm install

# Build frontend
npm run build

# Build backend for all platforms
mage buildAll

# Create distribution package
tar -czf open-llmengineer2-panel.tar.gz -C dist .
```

## 🐛 Troubleshooting

### Plugin not loading
- Check `allow_loading_unsigned_plugins` is configured
- Verify plugin files are in `/var/lib/grafana/plugins/open-llmengineer2-panel/`
- Check Grafana logs: `sudo journalctl -u grafana-server -f`

### API key errors
- Verify `GEMINI_API_KEY` environment variable is set
- Run: `sudo systemctl show grafana-server | grep GEMINI_API_KEY`
- Check API key is valid at [Google AI Studio](https://makersuite.google.com/)

### Permission issues
- Ensure plugin directory is owned by `grafana` user
- Run: `sudo chown -R grafana:grafana /var/lib/grafana/plugins/`

See [ENVIRONMENT_SETUP.md](ENVIRONMENT_SETUP.md) for more troubleshooting tips.

## 📄 License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Built with [Grafana Plugin SDK](https://github.com/grafana/grafana-plugin-sdk-go)
- Powered by [Google Gemini AI](https://ai.google.dev/)
- Screenshot capture using [html2canvas](https://html2canvas.hertzen.com/)

## 📞 Support

- 🐛 Report bugs via [GitHub Issues](https://github.com/your-username/open-llmengineer2-panel/issues)
- 💬 Ask questions in [Discussions](https://github.com/your-username/open-llmengineer2-panel/discussions)
- 📧 Contact: your-email@example.com

## 🗺️ Roadmap

- [ ] Support for additional LLM providers (OpenAI, Claude, etc.)
- [ ] Historical analysis and trend comparison
- [ ] Custom prompt templates
- [ ] Scheduled dashboard analysis
- [ ] Multi-language support
- [ ] Export analysis reports

---

**Made with ❤️ for the Grafana community**
