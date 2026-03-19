# Local Mercurial Practice with Heptapod

A local GitHub-like server for practicing Mercurial push/pull, running in Docker.

## Prerequisites

- Docker + Docker Compose installed
- Mercurial + hg-evolve installed:
  ```bash
  pixi global install mercurial --with hg-evolve
  ```
- Configure `~/.hgrc`:
  ```ini
  [ui]
  username = Your Name <your@email.com>

  [extensions]
  evolve =
  ```

---

## 1. Start the Server

```bash
docker compose up -d
```

It takes **2–3 minutes** to initialize. Watch the logs until you see `gitlab Reconfigured!`:

```bash
docker compose logs -f
```

Then open **http://localhost** in your browser.

---

## 2. First-Time Setup

1. Retrieve the auto-generated root password:
   ```bash
   docker exec heptapod cat /etc/gitlab/initial_root_password
   ```
   *(This file is automatically deleted 24 hours after first startup.)*
2. Log in as `root` with that password.
3. Go to **Admin Area → Users → New User** and create a regular account for yourself.
4. Log out of root, log in as your new user, and set a password when prompted.

---

## 3. Add Your SSH Key

```bash
# If you don't have one yet:
ssh-keygen -t ed25519 -C "your@email.com"

# Copy your public key:
cat ~/.ssh/id_ed25519.pub
```

In the Heptapod UI: click your avatar → **Preferences → SSH Keys** → paste it in.

Then test it:

```bash
ssh -p 2222 git@localhost
# Should say: "Welcome to GitLab, @yourusername!"
```

---

## 4. Create a Mercurial Repo

1. Click **New Project** in the UI.
2. Give it a name (e.g. `my-practice-repo`).
3. Look for the **Version control** option and select **Mercurial**. *(If you don't see this, go to Admin → Settings → Repository and enable Mercurial.)*
4. Click **Create project**.

---

## 5. Practice Push & Pull

**Clone your new repo:**
```bash
hg clone ssh://git@localhost:2222/yourusername/my-practice-repo
cd my-practice-repo
```

**Make a change and push:**
```bash
echo "hello mercurial" > hello.txt
hg add hello.txt
hg commit -m "my first commit"
hg push
```

**Pull changes (simulate working from another machine):**
```bash
# Clone a second copy somewhere else
hg clone ssh://git@localhost:2222/yourusername/my-practice-repo my-practice-repo-2
cd my-practice-repo-2
hg pull
hg update
```

---

## 6. Stopping & Starting

```bash
docker compose stop        # pause (keeps your data)
docker compose start       # resume
docker compose down        # remove containers (data volumes survive)
docker compose down -v     # ⚠️  wipe everything including repos
```

---

## Cheat Sheet

| Task | Command |
|---|---|
| Clone a repo | `hg clone <url>` |
| See current status | `hg status` |
| Stage a new file | `hg add <file>` |
| Commit | `hg commit -m "message"` |
| Push to server | `hg push` |
| Pull from server | `hg pull` |
| Apply pulled changes | `hg update` |
| See history | `hg log` |