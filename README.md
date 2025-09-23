![Banner image](https://user-images.githubusercontent.com/10284570/173569848-c624317f-42b1-45a6-ab09-f0ea3c247648.png)

# n8n - Secure Workflow Automation for Technical Teams

n8n is a workflow automation platform that gives technical teams the flexibility of code with the speed of no-code. With 400+ integrations, native AI capabilities, and a fair-code license, n8n lets you build powerful automations while maintaining full control over your data and deployments.

![n8n.io - Screenshot](https://raw.githubusercontent.com/n8n-io/n8n/master/assets/n8n-screenshot-readme.png)

## Key Capabilities

- **Code When You Need It**: Write JavaScript/Python, add npm packages, or use the visual interface
- **AI-Native Platform**: Build AI agent workflows based on LangChain with your own data and models
- **Full Control**: Self-host with our fair-code license or use our [cloud offering](https://app.n8n.cloud/login)
- **Enterprise-Ready**: Advanced permissions, SSO, and air-gapped deployments
- **Active Community**: 400+ integrations and 900+ ready-to-use [templates](https://n8n.io/workflows)

## Quick Start

Try n8n instantly with [npx](https://docs.n8n.io/hosting/installation/npm/) (requires [Node.js](https://nodejs.org/en/)):

```
npx n8n
```

Or deploy with [Docker](https://docs.n8n.io/hosting/installation/docker/):

```
docker volume create n8n_data
docker run -it --rm --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n docker.n8n.io/n8nio/n8n
```

Access the editor at http://localhost:5678

## Resources

- 📚 [Documentation](https://docs.n8n.io)
- 🔧 [400+ Integrations](https://n8n.io/integrations)
- 💡 [Example Workflows](https://n8n.io/workflows)
- 🤖 [AI & LangChain Guide](https://docs.n8n.io/langchain/)
- 👥 [Community Forum](https://community.n8n.io)
- 📖 [Community Tutorials](https://community.n8n.io/c/tutorials/28)

## Support

Need help? Our community forum is the place to get support and connect with other users:
[community.n8n.io](https://community.n8n.io)

## License

n8n is [fair-code](https://faircode.io) distributed under the [Sustainable Use License](https://github.com/n8n-io/n8n/blob/master/LICENSE.md) and [n8n Enterprise License](https://github.com/n8n-io/n8n/blob/master/LICENSE_EE.md).

- **Source Available**: Always visible source code
- **Self-Hostable**: Deploy anywhere
- **Extensible**: Add your own nodes and functionality

[Enterprise licenses](mailto:license@n8n.io) available for additional features and support.

Additional information about the license model can be found in the [docs](https://docs.n8n.io/reference/license/).

## Contributing

Found a bug 🐛 or have a feature idea ✨? Check our [Contributing Guide](https://github.com/n8n-io/n8n/blob/master/CONTRIBUTING.md) to get started.

## Join the Team

Want to shape the future of automation? Check out our [job posts](https://n8n.io/careers) and join our team!

## What does n8n mean?

**Short answer:** It means "nodemation" and is pronounced as n-eight-n.

**Long answer:** "I get that question quite often (more often than I expected) so I decided it is probably best to answer it here. While looking for a good name for the project with a free domain I realized very quickly that all the good ones I could think of were already taken. So, in the end, I chose nodemation. 'node-' in the sense that it uses a Node-View and that it uses Node.js and '-mation' for 'automation' which is what the project is supposed to help with. However, I did not like how long the name was and I could not imagine writing something that long every time in the CLI. That is when I then ended up on 'n8n'." - **Jan Oberhauser, Founder and CEO, n8n.io**

## Exposing n8n to the Internet with Cloudflare Tunnel

This section describes how to expose your locally running n8n Docker service to the internet securely using Cloudflare Tunnel. This method avoids opening any inbound ports on your firewall.

### Prerequisites

1.  A Cloudflare account with a registered domain.
2.  `docker` and `docker-compose` installed on your system.

### Configuration Steps

1.  **Update `docker-compose.yml`**:
    The `docker-compose.yml` file has been modified to include a `cloudflared` service and to configure the `n8n` service for public access via the tunnel.

    The key changes are:
    *   **`cloudflared` service**: A new service using the `cloudflare/cloudflared:latest` image. It connects to the `n8n` service internally via `http://n8n:5678`.
    *   **`n8n` service environment variables**: `N8N_HOST`, `WEBHOOK_URL`, and `N8N_EDITOR_BASE_URL` are set to `https://${N8N_PUBLIC_HOSTNAME}/`, and `N8N_PROTOCOL` is set to `https` to reflect the public HTTPS access provided by Cloudflare.

2.  **Create a `.env` file**:
    In the root of your project, create a file named `.env` with the following content. Replace `YOUR_CLOUDFLARE_TUNNEL_TOKEN` with the actual token obtained from your Cloudflare dashboard and `n8n.yourdomain.com` with your desired public hostname.

    ```dotenv
    CLOUDFLARE_TUNNEL_TOKEN="YOUR_CLOUDFLARE_TUNNEL_TOKEN"
    N8N_PUBLIC_HOSTNAME="n8n.yourdomain.com"
    ```

3.  **Generate Cloudflare Tunnel Token**:
    *   Log in to your Cloudflare dashboard.
    *   Navigate to "Workers & Pages" -> "Overview" -> "Create application" -> "Tunnels".
    *   Follow the instructions to create a new tunnel. Cloudflare will provide you with a `TUNNEL_TOKEN`. Copy this token and paste it into your `.env` file.

4.  **Configure Public Hostname in Cloudflare Dashboard**:
    *   On the "Public Hostnames" tab for your tunnel in the Cloudflare dashboard, add a new public hostname.
    *   Set the **Subdomain** (e.g., `n8n`) and **Domain** (e.g., `yourdomain.com`).
    *   For the **Service** section:
        *   Set **Type** to `HTTP`.
        *   Set **URL** to `http://n8n:5678`.

    **Important:** It's crucial that the **Service Type** is `HTTP` in the Cloudflare dashboard, as your `n8n` Docker service is listening on HTTP internally. Cloudflare will handle the external HTTPS connection.

5.  **Run Docker Compose**:
    Once the `.env` file is set up and the Cloudflare Tunnel is configured, start your Docker services:

    ```bash
    docker-compose up -d
    ```

    This will start the `n8n` and `cloudflared` containers, making your n8n instance accessible via your Cloudflare Tunnel at `https://n8n.yourdomain.com`.

### Troubleshooting

*   **502 Bad Gateway / `tls: first record does not look like a TLS handshake` error**: This usually indicates a mismatch between the **Service Type** configured in the Cloudflare dashboard and the internal protocol of the `n8n` service. Ensure the Cloudflare dashboard's "Service Type" for your tunnel is set to `HTTP` (since `n8n` listens on HTTP internally), even though external access will be HTTPS.
*   **Containers not starting**: Check `docker-compose logs <service_name>` (e.g., `docker-compose logs n8n` or `docker-compose logs cloudflared`) for error messages.