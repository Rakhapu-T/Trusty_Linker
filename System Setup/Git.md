---
tags:
  - git
---


# 0. Create & Add SSH keys to GitHub For Authentication
Instructions are outlined in the video below:
![](https://www.youtube.com/watch?v=yVP3sYgd0bY)

The video is summarized in the steps below:
1. In the terminal, enter the following command:
   ```bash
   ssh-keygen -t ed25519 -C "rakhaput1@gmail.com"
   ```
2. Use default file, unless it already exists
3. Do not enter passphrase
4. Copy the public key:
   ```bash
   cat ~/.ssh/id_ed25519.pub
   ```
5. Add SSH key to github
   a) Github -> settings
   b) SSH and GPG
   c) New SSH key
   d) Paste the key
6. Proceed with remote github tasks like push, pull, clone, e.t.c..

---
# Cheat-Sheet
Link: [[🚀 Git Complete Workflow Cheat Sheet]]
 

