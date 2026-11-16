---
tags:
  - tutorial
  - linker
  - Cheat_Sheet
---

# Installing Toolboxes on Linux

**The Issue:** When installing toolboxes on a standard Linux installation (usually in `/usr/local/MATLAB/`), users encounter permission errors because the directory is owned by `root`. Attempting to run MATLAB as `sudo` to bypass this often results in a **License Manager Error -9**, as the license is tied to the specific user account and not the root user.

**The Solution:** Use the MATLAB package manager (mpm) to install the desired toolbox from the terminal.
1. **Download mpm** 
```bash
wget https://www.mathworks.com/mpm/glnxa64/mpm
chmod +x mpm
```
    
2. **Install the desired toolbox** 
```bash
./mpm install --release=R2025b --products MY_REQUIRED_Toolbox --destination=/home/MY_USER/MY/PATH/TO/MATLAB
```

**SOURCE:** https://www.mathworks.com/matlabcentral/answers/2177759-toolbox-installation-going-wrong-linux

---

