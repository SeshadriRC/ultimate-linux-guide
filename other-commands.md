## Alias

You can achieve this by creating a **shortcut command (alias or function)** in your shell.

Since you’re using **WSL (Ubuntu/Linux on Windows)**, the cleanest way is to add a **shell function** in your `~/.bashrc` or `~/.zshrc`.

---

## Option 1: Using a shell function (recommended)

Open your shell config file:

```bash
nano ~/.bashrc
```

Add this at the bottom:

```bash
aws-scripts() {
    cd /mnt/e/WSL/AWS/aws-devops-zero-to-hero/My-Activities/Scripts || return
}
```

Reload config:

```bash
source ~/.bashrc
```

Now you can simply run:

```bash
aws-scripts
```

➡ It will directly take you to:

```
/mnt/e/WSL/AWS/aws-devops-zero-to-hero/My-Activities/Scripts
```

