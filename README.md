# NeteaseCloudMusicDownload
一个简易的网易云音乐单曲下载器
目前正考虑实现下载歌单功能
难得上传了，代码写在这
import requests
import time

# 下载歌曲
def download_song(song_name, song_id):
    # 构造歌曲的下载链接
    url = f"https://music.163.com/song/media/outer/url?id={song_id}.mp3"
    print(f"正在下载: {song_name}...")

    # 发起请求
    response = requests.get(url, stream=True)

    if response.status_code != 200:
        print("下载失败，服务器返回状态码:", response.status_code)
        return

    total_size = int(response.headers.get('content-length', 0))  # 获取总大小（字节）
    downloaded_size = 0  # 已下载大小
    start_time = time.time()  # 开始时间

    # 使用二进制写入模式打开文件
    with open(f"{song_name}.mp3", "wb") as f:
        for chunk in response.iter_content(chunk_size=8192):  # 每次读取 8KB
            if chunk:
                f.write(chunk)
                downloaded_size += len(chunk)

                elapsed_time = time.time() - start_time  # 已用时间
                speed = downloaded_size / elapsed_time / 1024  # KB/s

                # 打印进度和下载速度
                print(f"\r已下载: {downloaded_size // 1024} KB / {total_size // 1024} KB | 速度: {speed:.2f} KB/s", end="")

    print(f"\n下载完成! 已保存为: {song_name}.mp3\n")

if __name__ == "__main__":

    while True:
        song_name = input("请输入歌曲名称: ")
        song_id = input("请输入歌曲ID: ")
        download_song(song_name, song_id)

