### 字符串与字节数组的转换

```javascript
1、将字符转换成byte数组
     String  str = "罗长";
     byte[] sb = str.getBytes();

2、将byte数组转换成字符
     byte[] b={(byte)0xB8,(byte)0xDF,(byte)0xCB,(byte)0xD9}; 
     String str= new String (b);
```





### startsWith

使用startsWith(String prefix, int toffset) 来实现从某一偏移量开始的字符串比较，endwith也使用这个方法。

```java
    public boolean startsWith(String prefix, int toffset) {
        if (toffset >= 0 && toffset <= this.length() - prefix.length()) {
            byte[] ta = this.value;
            byte[] pa = prefix.value;
            int po = 0;
            int pc = pa.length;
            if (this.coder() == prefix.coder()) {
                int var7 = this.isLatin1() ? toffset : toffset << 1;

                while(po < pc) {
                    if (ta[var7++] != pa[po++]) {
                        return false;
                    }
                }
            } else {
                if (this.isLatin1()) {
                    return false;
                }

                while(po < pc) {
                    if (StringUTF16.getChar(ta, toffset++) != (pa[po++] & 255)) {
                        return false;
                    }
                }
            }

            return true;
        } else {
            return false;
        }
    }

    public boolean startsWith(String prefix) {
        return this.startsWith(prefix, 0);
    }

    public boolean endsWith(String suffix) {
        return this.startsWith(suffix, this.length() - suffix.length());
    }

```

