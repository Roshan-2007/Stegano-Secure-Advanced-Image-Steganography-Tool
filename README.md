import argparse
import hashlib
from PIL import Image
from cryptography.fernet import Fernet
import base64
import os

# ==============================
# 🔐 CRYPTO (Password)
# ==============================

def derive_key(password: str) -> bytes:
    """Derive a Fernet key from password"""
    key = hashlib.sha256(password.encode()).digest()
    return base64.urlsafe_b64encode(key)

def encrypt_data(data: bytes, password: str) -> bytes:
    key = derive_key(password)
    return Fernet(key).encrypt(data)

def decrypt_data(data: bytes, password: str) -> bytes:
    key = derive_key(password)
    return Fernet(key).decrypt(data)

# ==============================
# 🧠 HEADER
# ==============================

def create_header(data_len: int, encrypted: bool, is_file: bool, filename: str = ""):
    return f"STEG|{data_len}|{int(encrypted)}|{int(is_file)}|{filename}|".encode()

def parse_header(text: str):
    try:
        parts = text.split("|")
        if parts[0] != "STEG":
            return None
        return {
            "size": int(parts[1]),
            "encrypted": bool(int(parts[2])),
            "is_file": bool(int(parts[3])),
            "filename": parts[4]
        }
    except:
        return None

# ==============================
# 🔁 BINARY HELPERS
# ==============================

def to_binary(data: bytes):
    return ''.join(format(byte, '08b') for byte in data)

def from_binary(binary):
    return bytes(int(binary[i:i+8], 2) for i in range(0, len(binary), 8))

# ==============================
# 🔐 ENCODE
# ==============================

def encode(input_img, output_img, data: bytes, password=None, filename=""):
    encrypted = False
    is_file = bool(filename)

    if password:
        data = encrypt_data(data, password)
        encrypted = True

    header = create_header(len(data), encrypted, is_file, filename)
    full_data = header + data

    binary_data = to_binary(full_data)

    img = Image.open(input_img)
    pixels = img.load()

    w, h = img.size
    idx = 0

    for y in range(h):
        for x in range(w):
            pixel = list(pixels[x, y])

            for i in range(3):
                if idx < len(binary_data):
                    pixel[i] = pixel[i] & ~1 | int(binary_data[idx])
                    idx += 1

            pixels[x, y] = tuple(pixel)

            if idx >= len(binary_data):
                img.save(output_img)
                print("✅ Data hidden successfully!")
                return

    print("❌ Image too small!")

# ==============================
# 🔓 DECODE
# ==============================

def decode(image_path, password=None):
    img = Image.open(image_path)
    pixels = img.load()

    w, h = img.size
    binary_data = ""

    for y in range(h):
        for x in range(w):
            for i in range(3):
                binary_data += str(pixels[x, y][i] & 1)

    raw_data = from_binary(binary_data)
    text_data = raw_data.decode(errors="ignore")

    header = parse_header(text_data)
    if not header:
        print("❌ No hidden data found")
        return

    # Find payload start
    parts = text_data.split("|", 5)
    header_len = len("|".join(parts[:5])) + 1

    payload = raw_data[header_len:header_len + header["size"]]

    if header["encrypted"]:
        if not password:
            print("❌ Password required!")
            return
        payload = decrypt_data(payload, password)

    if header["is_file"]:
        filename = header["filename"] or "output_file"
        with open(filename, "wb") as f:
            f.write(payload)
        print(f"✅ File extracted: {filename}")
    else:
        print("✅ Hidden Message:")
        print(payload.decode(errors="ignore"))

# ==============================
# 🖥️ CLI
# ==============================

def main():
    parser = argparse.ArgumentParser(description="🔐 Stegano Secure (All-in-One)")

    subparsers = parser.add_subparsers(dest="command")

    # Hide
    hide_parser = subparsers.add_parser("hide")
    hide_parser.add_argument("--input", required=True)
    hide_parser.add_argument("--output", required=True)
    hide_parser.add_argument("--message")
    hide_parser.add_argument("--file")
    hide_parser.add_argument("--password")

    # Reveal
    reveal_parser = subparsers.add_parser("reveal")
    reveal_parser.add_argument("--image", required=True)
    reveal_parser.add_argument("--password")

    args = parser.parse_args()

    if args.command == "hide":
        if args.file:
            with open(args.file, "rb") as f:
                data = f.read()
            filename = os.path.basename(args.file)
        elif args.message:
            data = args.message.encode()
            filename = ""
        else:
            print("❌ Provide --message or --file")
            return

        encode(args.input, args.output, data, args.password, filename)

    elif args.command == "reveal":
        decode(args.image, args.password)

    else:
        parser.print_help()

if __name__ == "__main__":
    main()
