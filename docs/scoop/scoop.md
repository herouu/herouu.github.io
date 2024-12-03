### scoop <!-- {docsify-ignore-all} -->

#### scoop安装

```bash
Set-ExecutionPolicy RemoteSigned -scope CurrentUser

$env:SCOOP='D:\app\scoop'
[Environment]::SetEnvironmentVariable('SCOOP', $env:SCOOP, 'User')

irm https://herouu.github.io/shell/scoop/install.ps1 | iex
```

```bash
# 国内使用scoop
https://github.com/duzyn/scoop-cn
https://github.com/lzwme/scoop-proxy-cn

```

#### bucket自定义

* [Scoop bucket](<https://ericzong.github.io/posts/tool-scoop-bucket.html>)

