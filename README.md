# 🔐 Stegano-Secure: Advanced Image Steganography Tool

A powerful and secure **image steganography tool** built in Python that allows you to hide and extract secret data inside images using LSB (Least Significant Bit) encoding with optional encryption.

---

## 🚀 Features

* 🖼️ Hide secret messages inside images
* 🔓 Extract hidden data from images
* 🔐 Optional encryption using AES (Fernet)
* 🧠 Custom **steganographic header** for metadata handling
* 🖥️ CLI interface using Typer
* 📦 Modular and scalable architecture
* ⚡ Fast and lightweight

---

## 🛠️ Tech Stack

* Python
* Pillow (Image Processing)
* Typer (CLI Development)
* Cryptography (Encryption)

---

## 📁 Project Structure

```
stegano-secure/
│── main.py
│── steg/
│   ├── encoder.py
│   ├── decoder.py
│   ├── crypto.py
│   └── utils.py
│── requirements.txt
│── README.md
```

---

## ⚙️ Installation

```bash
git clone https://github.com/yourusername/stegano-secure.git
cd stegano-secure
pip install -r requirements.txt
```

---

## ▶️ Usage

### 🔐 Hide Data

```bash
python main.py hide --input input.png --output output.png --message "Secret Message"
```

### 🔓 Reveal Data

```bash
python main.py reveal --image output.png
```

---

## 🧠 How It Works

The tool embeds data into image pixels using LSB encoding.

### Header Format:

```
STEG|SIZE|ENCRYPTED|
```

* `STEG` → Identifier
* `SIZE` → Payload size
* `ENCRYPTED` → 0 or 1

This ensures accurate extraction and decoding of hidden data.

---

## 🔒 Security Features

* AES-based encryption (Fernet)
* Hidden metadata header
* Binary-level encoding
* Resistant to casual inspection

---

## 🚀 Future Improvements

* 📂 File embedding (PDF, ZIP, etc.)
* 🔑 Password-based key derivation (PBKDF2)
* 🧪 SHA-256 integrity verification
* 🖥️ GUI (Tkinter/Kivy)
* ☁️ Cloud integration
* 🐳 Docker support

---

## 📌 Use Cases

* Secure communication
* Digital watermarking
* Data hiding & cybersecurity learning
* Capture-the-Flag (CTF) challenges

---

## 🤝 Contributing

Contributions are welcome! Feel free to fork and submit pull requests.

---

## 📜 License

This project is open-source and available under the MIT License.

---

## 👨‍💻 Author

Developed by **Roshan Sheriff U**

---

⭐ If you like this project, don’t forget to star the repository!
