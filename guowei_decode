import os
import shutil
import zlib

PM9SCREW = b'\tPM9SCREW\t'
PM9SCREW_LEN = len(PM9SCREW)
pm9screw_mycryptkey = [2017, 7, 25, 11, 53]
cryptkey_len = len(pm9screw_mycryptkey)


def decrypt(path, write=True):
    try:
        # 使用上下文管理器读取文件内容
        with open(path, 'rb') as f:
            data = bytearray(f.read())

        if len(data) < PM9SCREW_LEN:
            print(f"Error: File {path} is too short.")
            return False

        if data[:PM9SCREW_LEN] != PM9SCREW:
            print(f"Error: File {path} does not have the expected header.")
            return False
        
        data = data[PM9SCREW_LEN:]
        data_len = len(data)
        
        # 解密过程
        out = bytearray(data_len)
        for i in range(data_len):
            out[i] = (pm9screw_mycryptkey[(data_len - i) % cryptkey_len]
                      ^ (~data[i])) % 256
        
        # 解压数据
        try:
            new = zlib.decompress(out)
        except zlib.error as e:
            print(f"Error: Decompression failed for {path}. {e}")
            return False

        if write:
            # 备份原文件并写入新内容
            backup_path = f"{path}.bak"
            shutil.move(path, backup_path)
            with open(path, 'wb') as f:
                f.write(new)
            print(f"Successfully decrypted and wrote to {path}. Backup saved as {backup_path}.")
        else:
            print(new)

    except Exception as e:
        print(f"Error processing file {path}: {e}")


def multi_decrypt(path):
    if not os.path.exists(path):
        print(f"Error: {path} not found.")
        return

    if os.path.isdir(path):
        # 遍历文件夹中的文件
        for root, dirs, files in os.walk(path):
            for f in files:
                if f.endswith('.php'):
                    decrypt(os.path.join(root, f), write=True)
    else:
        decrypt(path)


if __name__ == '__main__':
    multi_decrypt('./')
