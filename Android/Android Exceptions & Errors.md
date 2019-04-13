> Unknown failure (at android.os.Binder.execTransact(Binder.java:731)) Error while Installing APKs

出现这个问题的原因，是因为在早期，Android操作系统几乎只支持一种CPU架构：ARMv5。但是现在Android系统目前支持七种不同的CPU体系结构：ARMV5、ARMV7（2010）、X86（2011）、MIPS（2012）、ARMV8、MIPS64和X86Y64（2014）。它们中的每一个都与各自的ABI相关联。所以关于.so文件的处理需要更多的关注。 
解决方法如下： 

在build.gradle(Module:app)文件里面的defaultConfig模块下加入splits这一段就行，如下：

```dsl
defaultConfig {

 splits {
            abi {
                enable true
                reset()
                include 'x86', 'armeabi-v7a','x86_64'
                universalApk true
            }
	}
}
```

