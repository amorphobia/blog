如果想要使用 PowerShell 调用 DLL 中的函数，需要借助一点 C# 的力量。

# P/Invoke (平台调用)

具体介绍可以看微软的[文档](https://learn.microsoft.com/dotnet/standard/native-interop/pinvoke)。

# 以 libgit2 为例

需要注意的是，没办法直接从 PowerShell 脚本传递整型的指针，这里定义一个 C# 函数来代劳。

```PowerShell
$signature = @'
[DllImport("git2.dll", CallingConvention = CallingConvention.Cdecl)]
internal static extern int git_libgit2_version(ref int major, ref int minor, ref int rev);

public static Tuple<int, int, int> GitLibgit2Version() {
    int major = 0;
    int minor = 0;
    int rev = 0;
    git_libgit2_version(ref major, ref minor, ref rev);
    return Tuple.Create(major, minor, rev);
}
'@

$git2 = Add-Type -MemberDefinition $signature -Name libgit2 -Namespace libgit2 -PassThru

$git2::GitLibgit2Version()

# 输出
Item1 Item2 Item3 Length
----- ----- ----- ------
    1     9     0      3
```

这只是初步尝试的一个小例子，更实用的还是定义、传递结构体，待今后探索。