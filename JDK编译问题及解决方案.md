# strncpy()问题
## 问题描述
```shell
In function ‘char* strncpy(char*, const char*, size_t)’,
    inlined from ‘static jint Arguments::parse_each_vm_init_arg(const JavaVMInitArgs*, bool*, JVMFlag::Flags)’ at /home/fortandh/workspace/jdk12/src/hotspot/share/runtime/arguments.cpp:2472:29:
/usr/include/x86_64-linux-gnu/bits/string_fortified.h:106:34: error: ‘char* __builtin_strncpy(char*, const char*, long unsigned int)’ output truncated before terminating nul copying as many bytes from a string as its length [-Werror=stringop-truncation]
  106 |   return __builtin___strncpy_chk (__dest, __src, __len, __bos (__dest));
      |          ~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/fortandh/workspace/jdk12/src/hotspot/share/runtime/arguments.cpp: In static member function ‘static jint Arguments::parse_each_vm_init_arg(const JavaVMInitArgs*, bool*, JVMFlag::Flags)’:
/home/fortandh/workspace/jdk12/src/hotspot/share/runtime/arguments.cpp:2471:44: note: length computed here
 2471 |         size_t len = (pos == NULL) ? strlen(tail) : pos - tail;
      |                                      ~~~~~~^~~~~~
cc1plus: all warnings being treated as errors
make[3]: *** [lib/CompileJvm.gmk:174: /home/fortandh/workspace/jdk12/build/linux-x86_64-server-release/hotspot/variant-server/libjvm/objs/arguments.o] Error 1
make[2]: *** [make/Main.gmk:257: hotspot-server-libs] Error 2
```

## 原因分析
错误的代码如下所示：
```
char* name = strncpy(NEW_C_HEAP_ARRAY(char, len + 1, mtArguments), tail, len);
```
一般来说，strncpy()不建议使用，因为可能会发生潜在的截断问题，一般用memcpy()替代

## 解决方案
```
// char* name = strncpy(NEW_C_HEAP_ARRAY(char, len + 1, mtArguments), tail, len);
char* name = NEW_C_HEAP_ARRAY(char, len + 1, mtArguments);
memcpy(name, tail, len);
```

# 空字符串无法用%s输出问题
## 问题描述
```shell
/home/fortandh/workspace/jdk12/src/hotspot/share/utilities/debug.hpp:100:20: error: ‘%s’ directive argument is null [-Werror=format-overflow=]
  100 |     report_vm_error(__FILE__, __LINE__, "guarantee(" #p ") failed", __VA_ARGS__); \
      |     ~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/fortandh/workspace/jdk12/src/hotspot/cpu/x86/macroAssembler_x86.hpp:117:7: note: in expansion of macro ‘guarantee’
  117 |       guarantee(this->is8bit(imm8), "Short forward jump exceeds 8-bit offset at %s:%d", file, line);
      |       ^~~~~~~~~
In file included from /home/fortandh/workspace/jdk12/src/hotspot/share/asm/macroAssembler.hpp:31,
                 from /home/fortandh/workspace/jdk12/src/hotspot/share/asm/assembler.cpp:27:
/home/fortandh/workspace/jdk12/src/hotspot/cpu/x86/macroAssembler_x86.hpp: In member function ‘void Label::patch_instructions(MacroAssembler*)’:
/home/fortandh/workspace/jdk12/src/hotspot/cpu/x86/macroAssembler_x86.hpp:117:81: note: format string is defined here
  117 |       guarantee(this->is8bit(imm8), "Short forward jump exceeds 8-bit offset at %s:%d", file, line);
      |                                                                                 ^~
```

## 原因分析
错误的代码如下所示：
```
guarantee(this->is8bit(imm8), "Short forward jump exceeds 8-bit offset at %s:%d", file, line);
```

问题在于没有考虑file变量是否为NULL

## 解决方案
```
// guarantee(this->is8bit(imm8), "Short forward jump exceeds 8-bit offset at %s:%d", file, line);
guarantee(this->is8bit(imm8), "Short forward jump exceeds 8-bit offset at %s:%d",file == NULL ? "<NULL>" : file, line);
```
