
# Run TRELLIS on Google Colab with GPU (Python 3.10 Compatibility)

This guide explains how to run the [TRELLIS](https://huggingface.co/spaces/trellis-community/TRELLIS) app locally in Google Colab using a GPU. TRELLIS does **not run properly on Python 3.11.13**, which is the default Python version in Google Colab. To resolve this, we downgrade to **Python 3.10.12**, configure the environment, and then launch the app.

## ✅ Why This Setup?

* **TRELLIS GPU usage on Hugging Face Spaces is limited.**
* Running it on **Google Colab Pro** allows extended GPU usage and repeated testing.
* The default Python 3.11.13 on Colab causes errors — TRELLIS runs well on **Python 3.10.12**.

---

## 🔧 Steps to Set Up TRELLIS on Colab

### 1. Downgrade to Python 3.10

Colab uses Python 3.11+ by default, which isn't compatible with TRELLIS. This block installs Python 3.10 and sets it as the system default:

```bash
# Install Python 3.10 and required packages
!sudo apt-get update -y
!sudo apt-get install python3.10 python3.10-distutils python3.10-dev -y

# Set Python 3.10 as the default Python version
!sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.10 1
!sudo update-alternatives --set python3 /usr/bin/python3.10

# Install pip for Python 3.10
!curl -sS https://bootstrap.pypa.io/get-pip.py | python3.10
```

---

### 2. Clone the TRELLIS Repository

Install Git LFS (Large File Storage) and clone the TRELLIS repo from Hugging Face:

```bash
!apt install git-lfs -y
!git lfs install
!git clone https://huggingface.co/spaces/trellis-community/TRELLIS
```

---

### 3. Patch the TRELLIS Code for Local GPU Use

To remove Hugging Face-specific dependencies (like `spaces`), we update the app's Python script:

```bash
# Remove "import spaces" from app.py
!sed -i '/import spaces/d' app.py

# Remove decorators "@spaces.GPU"
!sed -i '/@spaces.GPU/d' app.py
```

These lines remove any Hugging Face-specific integrations that aren't needed when running locally on Colab.

---

### 4. Prevent Backend Conflicts and Modify Launch Behavior

```python
import os
os.environ.pop("MPLBACKEND", None)  # Remove Matplotlib backend env if set
```

```bash
# Replace `mcp_server=True` with `share=True` for public access
!sed -i 's/demo.launch(mcp_server=True)/demo.launch(share=True)/g' app.py

# Launch the app using Python 3.10 and Agg backend for Matplotlib
!MPLBACKEND=Agg python3.10 app.py
```

---

## 🚀 Final Result

After completing these steps:

* TRELLIS runs in your Google Colab environment.
* It uses **your Colab GPU** instead of limited Hugging Face GPU.
* You can run the app **multiple times** to test various images and outputs.

---

## ✅ Summary

This method provides:

* **Extended GPU usage** with Colab Pro
* **Python 3.10 compatibility** for apps not working on newer versions
* A way to **remove Hugging Face Space dependencies** for local deployment

