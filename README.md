# Remote Claude Code on VPS

Run Claude Code on a VPS and access it from your phone, tablet, or any device with a terminal app. Code from anywhere.

## What You'll Need

- A VPS (Hostinger, Hetzner, DigitalOcean, etc.) - $5/mo tier is plenty
- 4GB RAM minimum, 8GB+ recommended
- A mobile SSH client (Termius recommended)

---

## Quick Start

### 1. Spin Up Your VPS

If using **Hostinger**, select the "Claude Code" app template during setup - it comes pre-installed.

For manual installation on any VPS:

```bash
# SSH into your VPS
ssh root@your-vps-ip

# Install Node.js and npm
apt update && apt install -y nodejs npm

# Install Claude Code globally
npm install -g @anthropic-ai/claude-code

# Start Claude Code
claude
```

---

### 2. Secure Your VPS (Important!)

Before accessing from mobile, lock down your server:

```bash
# Disable password authentication (SSH keys only)
sudo sed -i 's/^#*PasswordAuthentication.*/PasswordAuthentication no/' \
  /etc/ssh/sshd_config /etc/ssh/sshd_config.d/*.conf && \
  sudo systemctl restart ssh

# Install fail2ban to block brute force attacks
sudo apt install fail2ban -y

# Enable firewall - only allow SSH
sudo ufw allow 22 && sudo ufw enable
```

---

### 3. Set Up Mobile Access

1. Download **Termius** on your phone (iOS/Android) - it's free
2. Add a new host with your VPS IP address
3. Connect using your SSH key or password

---

### 4. Session Persistence with tmux

Keep your Claude Code session alive even when your phone disconnects:

```bash
# Install tmux
sudo apt install tmux -y

# Start a new named session
tmux new -s claude

# Run Claude Code inside the session
claude
```

**Enable mouse scrolling** (so you can scroll with your finger):

```bash
echo "set -g mouse on" >> ~/.tmux.conf && tmux source-file ~/.tmux.conf
```

**Reconnect to your session** after disconnect:

```bash
tmux attach -t claude
```

---

## Quick Reference

| Command | What it does |
|---------|--------------|
| `tmux new -s claude` | Start a new tmux session named "claude" |
| `tmux attach -t claude` | Reconnect to your session |
| `tmux ls` | List all sessions |
| `Ctrl+B, D` | Detach from session (leave it running) |

---

## Troubleshooting

### OAuth/Authentication Issues on Headless VPS

If Claude Code can't complete OAuth login:

```bash
# Option 1: SSH port forwarding (run from your local machine)
ssh -L 15735:localhost:15735 user@your-vps-ip

# Option 2: Use API key directly
export ANTHROPIC_API_KEY=your-key-here
claude
```

### Can't Scroll in tmux on Mobile

Make sure mouse mode is enabled:

```bash
echo "set -g mouse on" >> ~/.tmux.conf
tmux source-file ~/.tmux.conf
```

### Session Dies When Phone Sleeps

This is why we use tmux! Your session keeps running. Just reconnect with:

```bash
tmux attach -t claude
```

### Memory Issues on Long Sessions

Claude Code can use significant memory. Monitor with `htop` and restart Claude periodically if needed.

---

## VPS Recommendations

| Provider | Price | Notes |
|----------|-------|-------|
| Hostinger | ~$5/mo | Has Claude Code pre-installed template |
| Hetzner | ~$5/mo | Great value, Europe-focused |
| Vultr | ~$12/mo | Fast performance |
| DigitalOcean | ~$6/mo | Developer-friendly |

---

## Resources

- [Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)
- [Termius](https://termius.com/) - Mobile SSH client
- [tmux Cheat Sheet](https://tmuxcheatsheet.com/)

---

## License

MIT - Do whatever you want with this.
