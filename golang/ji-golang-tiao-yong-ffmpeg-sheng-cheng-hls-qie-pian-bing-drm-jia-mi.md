# 记golang调用ffmpeg生成hls切片并DRM加密

### 前言

最近在学习golang以及ffmpeg，刚好有朋友需要做视频视频加密，防盗链。

### DRM AES-128加密

加密是对视频内容的一种保护手段，有效防止视频泄露和倒链问题。AES加密主要是将所有ts内容进行加密，然后在m3u8文件中加入 `#EXT-X-KEY`这个tag，一般这个tag会提供一个获取key的地址用于播放器解密播放视频。

### ffmpeg

ffmpeg是一个非常强大的多媒体处理工具，能够解码、编码、转码、混合、解密、流媒体等。ffmpeg安装教程这里就不提供啦。

### 如何加密？

#### 命令篇

**生成KEY**

在加密前我们需要使用openssl生成密钥：

```bash
openssl rand 16 > ${keyFIlePath}
```

**生成IV**

然后生成IV：

```bash
openssl rand -hex 16
```

会得到一串字符串：

```bash
8fe92a02dcf1faa0ab4462b9f4a76ea1
```

**创建keyinfo文件**

接下来需要创建keyinfo,这个文件用于ffmpeg进行加密，格式：

```
获取key的URI
key文件路径
IV值（可选）
```

例如：

```
http://localhost:8199/keys/1634726192843.key
./public/assets/keys/1634726192843.key
49537040b6d82786afb00d9f5fe58953
```

**ffmpeg加密**

这里一行命令就搞定啦：

```bash
fmpeg -y -i ${filePath} -c:v copy -c:a copy -hls_time 10 -hls_list_size 0 -hls_key_info_file ${keyinfoPath} -hls_playlist_type vod ${outputFilePath}
```

参数：

* filePath：需要加密的视频路径，可以为url，rtmp流地址，本地视频路径
* keyinfoPath：上面的keyinfo路径
* outputFilePath：加密后的m3u8文件存放地址及文件名，以`.m3u8`结尾

例如：

```bash
ffmpeg -y -i ./public/assets/uploaded/test.mp4 -c:v copy -c:a copy -hls_time 10 -hls_list_size 0 -hls_key_info_file ./public/assets/keys/1634726359613.key.info -hls_playlist_type vod ./public/assets/mp4/2021-10-20/123.m3u8
```

执行后将生成m3u8文件及ts切片。

```bash
ls
123.m3u8 1230.ts  1232.ts  1234.ts  1236.ts  1238.ts
123.mp4  1231.ts  1233.ts  1235.ts  1237.ts
```

打开m3u8文件可看到内容：

```
#EXTM3U
#EXT-X-VERSION:3
#EXT-X-TARGETDURATION:12
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-KEY:METHOD=AES-128,URI="http://localhost:8199/keys/1634726359613.key",IV=0x1a7742e015431e6cc9e69600550b34a9
#EXTINF:12.208333,
1230.ts
#EXTINF:8.208333,
1231.ts
#EXTINF:10.916667,
1232.ts
#EXTINF:11.166667,
1233.ts
#EXTINF:8.666667,
1234.ts
#EXTINF:11.583333,
1235.ts
#EXTINF:7.500000,
1236.ts
#EXTINF:12.375000,
1237.ts
#EXTINF:10.250000,
1238.ts
#EXT-X-ENDLIST
```

解析格式：

```
#EXTM3U                    M3U8文件头，必须放在第一行;
#EXT-X-MEDIA-SEQUENCE      第一个TS分片的序列号，一般情况下是0，但是在直播场景下，这个序列号标识直播段的起始位置; #EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION      每个分片TS的最大的时长;   
#EXT-X-TARGETDURATION:10     每个分片的最大时长是 10s
#EXT-X-ALLOW-CACHE         是否允许cache;          
#EXT-X-ALLOW-CACHE:YES      
#EXT-X-ALLOW-CACHE:NO    默认情况下是YES
#EXT-X-ENDLIST             M3U8文件结束符；
#EXTINF                    extra info，分片TS的信息，如时长，带宽等；一般情况下是    
#EXTINF:<duration>,[<title>] 后面可以跟着其他的信息，逗号之前是当前分片的ts时长，分片时长 移动要小于 
#EXT-X-TARGETDURATION 定义的值；
#EXT-X-VERSION             M3U8版本号
#EXT-X-DISCONTINUITY       该标签表明其前一个切片与下一个切片之间存在中断。
#EXT-X-PLAYLIST-TYPE       表明流媒体类型；
#EXT-X-KEY                 是否加密解析，    
#EXT-X-KEY:METHOD=AES-128,URI="http://localhost:8199/keys/1634726359613.key"    加密方式是AES-128,秘钥需要请求   http://localhost:8199/keys/1634726359613.key  ，请求回来存储在本地；
```

#### 使用go调用ffmpeg

使用go调用命令行主要是使用os.exec包。

**生成KEY函数**

```go
func GenKeyFile() string {
  // 直接创建cmd命令
	cmd := exec.Command("openssl", "rand", "16")
  // 生成key文件名
	fileName := strings.Join([]string{strconv.FormatInt(time.Now().UnixNano()/1e6, 10), ".key"}, "")
  // 创建文件
	outfile, err := os.Create(fileName)
	if err != nil {
		panic(err)
	}
	defer outfile.Close()
  // 将cmd命令执行后的输出输出到文件内
	cmd.Stdout = outfile
	cmd.Stderr = os.Stderr
	// 开始执行命令
	err = cmd.Start()
	if err != nil {
		log.Fatalf("failed to call cmd.Run(): %v", err)
	}
  // 等待
	cmd.Wait()
	return fileName
}
```

**生成keyinfo**

```go
func GenKeyInfoFile(keyFileName string) string {
	// 直接创建cmd命令
	cmd := exec.Command("openssl", "rand", "-hex", "16")
	// 得到输出的字节数组
	byteData, err := cmd.Output()
  // 转为字符串
	hexKey := string(byteData)

	if err != nil {
		panic(err)
	}
  // 生成keyinfo文件名
	filename := strings.Join([]string{keyFileName, ".info"}, "")
  // 创建文件
	outfile, err := os.Create(filename)
	if err != nil {
		panic(err)
	}
	// 生成获取key的URL
	url := strings.Join([]string{config.KEY_REQUEST_URL, keyFileName}, "")
  // 拿到key的文件路径
	filePath := keyFileName
  // 写入keyinfo文件
	outfile.WriteString(strings.Join([]string{url, filePath, hexKey}, "\n"))
	return filename
}
```

**DRM加密**

```go
func DRMEncrypt(filePath string, keyinfoPath string, fileName string) (*exec.Cmd, string) {
  // 生成输出目录
	dirName := GetDirName()
  // 生成hls文件名
	hlsFileName := strings.Join([]string{fileName, ".m3u8"}, "")
  // 生成输出文件路径
	hlsFilePath := strings.Join([]string{config.MP4_PATH, dirName, hlsFileName}, "/")
	// 创建cmd对象
	cmd := exec.Command("ffmpeg", "-y", "-i", filePath, "-c:v", "copy", "-c:a", "copy", "-hls_time", "10", "-hls_list_size", "0", "-hls_key_info_file", keyinfoPath, "-hls_playlist_type", "vod", hlsFilePath)
  // 将ffmpeg命令执行输出到控制台
	cmd.Stdout = os.Stdout
	cmd.Stderr = os.Stderr
	err := cmd.Start()
	if err != nil {
		log.Fatalf("failed to call cmd.Run(): %v", err)
	}
	return cmd, strings.Join([]string{dirName, hlsFileName}, "/")
}
```

**main函数**

```go
func main(){
	filePath := "/assets/mp4/test.mp4"
	m3u8File:="/assets/hls/test.m3u8"
	keyFilename := util.GenKeyFile()
	keyInfoFilename := util.GenKeyInfoFile(keyFilename)
	cmd, hlsFileName := util.DRMEncrypt(filePath, keyInfoFilename, m3u8File)

	cmd.Wait()
	fmt.Println(hlsFileName)
}
```

**注意**

当传入直播流url时，ffmpeg会一直拉流，为了不阻塞线程，我们可以使用go关键字来实现非阻塞调用：

```go
	cmd, hlsFileName := util.DRMEncrypt(filePath, keyInfoFilename, m3u8File)
	
	go func() {
		cmd.Wait()
		fmt.Println(hlsFileName)
	}()
```

基于上面的go代码即可实现通过http接口进行视频加密，这段代码就不放了，至此就结束啦。
