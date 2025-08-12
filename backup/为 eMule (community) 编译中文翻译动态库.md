社区版 eMule 可以在 https://github.com/irwir/eMule/releases 页面下载，虽然源代码里有中文翻译的资源，但发布的二进制文件并没有将其编译，因此需要自行编译来使用。

因为我一般只安装 [Visual Studio 生成工具](https://visualstudio.microsoft.com/zh-hans/downloads/?q=build+tools#build-tools-for-visual-studio-2022)，所以记录一下我用命令行编译的方法。如果没有安装 MFC 相关模块，则需要把 `zh_CN.rc` 里引用 `afxres.h` 改为 引用 `Windows.h` 和 `winres.h`[^1]。然后，使用命令 `msbuild zh_CN.vcxproj /p:configuration=Dynamic /p:platform=x64` 来编译[^2]。

[^1]: https://web.archive.org/web/20240406120953/https://blog.csdn.net/duiwangxiaomi/article/details/88822702
[^2]: https://learn.microsoft.com/cpp/build/walkthrough-using-msbuild-to-create-a-visual-cpp-project?view=msvc-170#using-msbuild-with-build-properties