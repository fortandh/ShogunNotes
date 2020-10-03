# strncpy()截断字符串问题
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

## 问题描述
```
In function ‘char* strncpy(char*, const char*, size_t)’,
    inlined from ‘void DCmdArgument<ArgType>::parse_value(const char*, size_t, Thread*) [with ArgType = char*]’ at /home/fortandh/workspace/jdk12/src/hotspot/share/services/diagnosticArgument.cpp:183:12,
    inlined from ‘void DCmdArgument<ArgType>::init_value(Thread*) [with ArgType = char*]’ at /home/fortandh/workspace/jdk12/src/hotspot/share/services/diagnosticArgument.cpp:190:22:
/usr/include/x86_64-linux-gnu/bits/string_fortified.h:106:34: error: ‘char* __builtin_strncpy(char*, const char*, long unsigned int)’ output truncated before terminating nul copying as many bytes from a string as its length [-Werror=stringop-truncation]
  106 |   return __builtin___strncpy_chk (__dest, __src, __len, __bos (__dest));
      |          ~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/fortandh/workspace/jdk12/src/hotspot/share/services/diagnosticArgument.cpp: In member function ‘void DCmdArgument<ArgType>::init_value(Thread*) [with ArgType = char*]’:
/home/fortandh/workspace/jdk12/src/hotspot/share/services/diagnosticArgument.cpp:190:22: note: length computed here
  190 |     this->parse_value(_default_string, strlen(_default_string), THREAD);
      |     ~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
cc1plus: all warnings being treated as errors
```

## 原因分析
错误的代码如下：
```
strncpy(_value, str, len);
```

## 解决方案
```
// strncpy(_value, str, len);
memcpy(_value, str, len);
```

## 问题描述
```
In function ‘char* strncpy(char*, const char*, size_t)’,
    inlined from ‘void DCmdArgument<ArgType>::parse_value(const char*, size_t, Thread*) [with ArgType = bool]’ at /home/fortandh/workspace/jdk12/src/hotspot/share/services/diagnosticArgument.cpp:156:14,
    inlined from ‘void DCmdArgument<ArgType>::init_value(Thread*) [with ArgType = bool]’ at /home/fortandh/workspace/jdk12/src/hotspot/share/services/diagnosticArgument.cpp:166:22:
/usr/include/x86_64-linux-gnu/bits/string_fortified.h:106:34: error: ‘char* __builtin_strncpy(char*, const char*, long unsigned int)’ output truncated before terminating nul copying as many bytes from a string as its length [-Werror=stringop-truncation]
  106 |   return __builtin___strncpy_chk (__dest, __src, __len, __bos (__dest));
      |          ~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/fortandh/workspace/jdk12/src/hotspot/share/services/diagnosticArgument.cpp: In member function ‘void DCmdArgument<ArgType>::init_value(Thread*) [with ArgType = bool]’:
/home/fortandh/workspace/jdk12/src/hotspot/share/services/diagnosticArgument.cpp:166:22: note: length computed here
  166 |     this->parse_value(_default_string, strlen(_default_string), THREAD);
      |     ~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
cc1plus: all warnings being treated as errors
```

## 原因分析
错误的代码如下：
```
strncpy(buf, str, len);
```

## 解决方案
```
// strncpy(buf, str, len);
memcpy(buf, str, len);
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

# strncpy() 截断问题
## 问题描述
```
In function ‘char* strncpy(char*, const char*, size_t)’,
    inlined from ‘void ClassFileParser::prepend_host_package_name(const InstanceKlass*, Thread*)’ at /home/fortandh/workspace/jdk12/src/hotspot/share/classfile/classFileParser.cpp:5761:12:
/usr/include/x86_64-linux-gnu/bits/string_fortified.h:106:34: error: ‘char* __builtin_strncpy(char*, const char*, long unsigned int)’ output truncated before terminating nul copying as many bytes from a string as its length [-Werror=stringop-truncation]
  106 |   return __builtin___strncpy_chk (__dest, __src, __len, __bos (__dest));
      |          ~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/fortandh/workspace/jdk12/src/hotspot/share/classfile/classFileParser.cpp: In member function ‘void ClassFileParser::prepend_host_package_name(const InstanceKlass*, Thread*)’:
/home/fortandh/workspace/jdk12/src/hotspot/share/classfile/classFileParser.cpp:5756:33: note: length computed here
 5756 |     size_t host_pkg_len = strlen(host_pkg_name);
      |                           ~~~~~~^~~~~~~~~~~~~~~
cc1plus: all warnings being treated as errors
```

## 原因分析
出错代码如下：
```
strncpy(new_anon_name, host_pkg_name, host_pkg_len);
```
不建议使用strncpy函数，用memcpy函数替代

## 解决方案
```
// strncpy(new_anon_name, host_pkg_name, host_pkg_len);
memcpy(new_anon_name, host_pkg_name, host_pkg_len);
```

# strncat()截断字符串问题
## 问题描述
```
In function ‘char* strncat(char*, const char*, size_t)’,
    inlined from ‘static void ClassLoaderExt::process_jar_manifest(ClassPathEntry*, bool)’ at /home/fortandh/workspace/jdk12/src/hotspot/share/classfile/classLoaderExt.cpp:212:16:
/usr/include/x86_64-linux-gnu/bits/string_fortified.h:136:34: error: ‘char* __builtin_strncat(char*, const char*, long unsigned int)’ output truncated before terminating nul copying as many bytes from a string as its length [-Werror=stringop-truncation]
  136 |   return __builtin___strncat_chk (__dest, __src, __len, __bos (__dest));
      |          ~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/fortandh/workspace/jdk12/src/hotspot/share/classfile/classLoaderExt.cpp: In static member function ‘static void ClassLoaderExt::process_jar_manifest(ClassPathEntry*, bool)’:
/home/fortandh/workspace/jdk12/src/hotspot/share/classfile/classLoaderExt.cpp:206:33: note: length computed here
  206 |       int name_len = (int)strlen(file_start);
      |                           ~~~~~~^~~~~~~~~~~~
cc1plus: all warnings being treated as errors
```

## 原因分析
出错代码如下：
```
strncat(libname, file_start, name_len);
```
因为编译器无法识别name_len的长度是否满足要求，因此以warning的形式报出这里潜在的截断字符串问题

## 解决方案
```
// strncat(libname, file_start, name_len);
strncat(libname, file_start, sizeof(libname)-strlen(libname)-1);
```
