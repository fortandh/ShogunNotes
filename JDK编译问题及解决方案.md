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
```c++
char* name = strncpy(NEW_C_HEAP_ARRAY(char, len + 1, mtArguments), tail, len);
```
一般来说，strncpy()不建议使用，因为可能会发生潜在的截断问题，一般用memcpy()替代

## 解决方案
```c++
// char* name = strncpy(NEW_C_HEAP_ARRAY(char, len + 1, mtArguments), tail, len);
char* name = NEW_C_HEAP_ARRAY(char, len + 1, mtArguments);
memcpy(name, tail, len);
```

## 问题描述
```shell
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
```c++
strncpy(_value, str, len);
```

## 解决方案
```c++
// strncpy(_value, str, len);
memcpy(_value, str, len);
```

## 问题描述
```shell
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
```c++
strncpy(buf, str, len);
```

## 解决方案
```c++
// strncpy(buf, str, len);
memcpy(buf, str, len);
```

## 问题描述
```shell
nlined from ‘char* copy_path(const char*)’ at /home/fortandh/workspace/jdk12/src/hotspot/share/jfr/recorder/repository/jfrChunkState.cpp:103:10,
    inlined from ‘void JfrChunkState::set_path(const char*)’ at /home/fortandh/workspace/jdk12/src/hotspot/share/jfr/recorder/repository/jfrChunkState.cpp:115:22:
/usr/include/x86_64-linux-gnu/bits/string_fortified.h:106:34: error: ‘char* __builtin_strncpy(char*, const char*, long unsigned int)’ output truncated before terminating nul copying as many bytes from a string as its length [-Werror=stringop-truncation]
  106 |   return __builtin___strncpy_chk (__dest, __src, __len, __bos (__dest));
      |          ~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/fortandh/workspace/jdk12/src/hotspot/share/jfr/recorder/repository/jfrChunkState.cpp: In member function ‘void JfrChunkState::set_path(const char*)’:
/home/fortandh/workspace/jdk12/src/hotspot/share/jfr/recorder/repository/jfrChunkState.cpp:101:33: note: length computed here
  101 |   const size_t path_len = strlen(path);
      |                           ~~~~~~^~~~~~
cc1plus: all warnings being treated as errors
```

## 原因分析
错误的代码如下：
```c++
strncpy(new_path, path, path_len);
```

## 解决方案
```c++
// strncpy(new_path, path, path_len);
memcpy(new_path, path, path_len);
```

## 问题描述
```shell
In function ‘char* strncpy(char*, const char*, size_t)’,
    inlined from ‘void JfrOSInterface::JfrOSInterfaceImpl::functionality_not_implemented(char**) const’ at /home/fortandh/workspace/jdk12/src/hotspot/share/jfr/periodic/jfrOSInterface.cpp:186:10:
/usr/include/x86_64-linux-gnu/bits/string_fortified.h:106:34: error: ‘char* __builtin_strncpy(char*, const char*, long unsigned int)’ output truncated before terminating nul copying 29 bytes from a string of the same length [-Werror=stringop-truncation]
  106 |   return __builtin___strncpy_chk (__dest, __src, __len, __bos (__dest));
      |          ~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
cc1plus: all warnings being treated as errors
```

## 原因分析
错误的代码如下：
```c++
strncpy(*str, not_impl, not_impl_len);
```

## 解决方案
```c++
// strncpy(*str, not_impl, not_impl_len);
memcpy(*str, not_impl, not_impl_len);
```

**Note:后面碰到类似问题，将不再列出**

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
```c++
guarantee(this->is8bit(imm8), "Short forward jump exceeds 8-bit offset at %s:%d", file, line);
```

问题在于没有考虑file变量是否为NULL

## 解决方案
```c++
// guarantee(this->is8bit(imm8), "Short forward jump exceeds 8-bit offset at %s:%d", file, line);
guarantee(this->is8bit(imm8), "Short forward jump exceeds 8-bit offset at %s:%d",file == NULL ? "<NULL>" : file, line);
```

# strncat()截断字符串问题
## 问题描述
```shell
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
```c++
strncat(libname, file_start, name_len);
```
因为编译器无法识别name_len的长度是否满足要求，因此以warning的形式报出这里潜在的截断字符串问题

## 解决方案
```c++
// strncat(libname, file_start, name_len);
strncat(libname, file_start, sizeof(libname)-strlen(libname)-1);
```

# memset()清理对象问题
## 问题描述
```shell
/home/fortandh/workspace/jdk12/src/hotspot/share/compiler/disassembler.cpp: In constructor ‘decode_env::decode_env(CodeBlob*, outputStream*, CodeStrings, ptrdiff_t)’:
/home/fortandh/workspace/jdk12/src/hotspot/share/compiler/disassembler.cpp:365:32: error: ‘void* memset(void*, int, size_t)’ clearing an object of non-trivial type ‘class decode_env’; use assignment instead [-Werror=class-memaccess]
  365 |   memset(this, 0, sizeof(*this)); // Beware, this zeroes bits of fields.
      |                                ^
/home/fortandh/workspace/jdk12/src/hotspot/share/compiler/disassembler.cpp:153:7: note: ‘class decode_env’ declared here
  153 | class decode_env {
      |       ^~~~~~~~~~
cc1plus: all warnings being treated as errors
```

## 原因分析
出错的代码如下：
```c++
decode_env::decode_env(CodeBlob* code, outputStream* output, CodeStrings c,
                       ptrdiff_t offset) {
  memset(this, 0, sizeof(*this)); // Beware, this zeroes bits of fields.
```

memset中的指针参数要变成静态的

## 解决方案
```c++
decode_env::decode_env(CodeBlob* code, outputStream* output, CodeStrings c,
                       ptrdiff_t offset) : _nm(NULL),
                                           _start(NULL),
                                           _end(NULL),
                                           _option_buf(),
                                           _print_raw('\0'),
                                           _cur_insn(NULL) {
// memset(this, 0, sizeof(*this)); // Beware, this zeroes bits of fields.
```

# memset()给私有成员赋值问题
## 问题描述
```shell
/home/fortandh/workspace/jdk12/src/hotspot/share/gc/parallel/objectStartArray.cpp: In member function ‘void ObjectStartArray::set_covered_region(MemRegion)’:
/home/fortandh/workspace/jdk12/src/hotspot/share/gc/parallel/objectStartArray.cpp:106:56: error: ‘void* memset(void*, int, size_t)’ writing to an object of type ‘class HeapWord’ with ‘private’ member ‘HeapWord::i’ [-Werror=class-memaccess]
  106 |     memset(_blocks_region.end(), clean_block, expand_by);
      |                                                        ^
In file included from /home/fortandh/workspace/jdk12/src/hotspot/share/utilities/align.hpp:28,
                 from /home/fortandh/workspace/jdk12/src/hotspot/share/runtime/globals.hpp:29,
                 from /home/fortandh/workspace/jdk12/src/hotspot/share/memory/allocation.hpp:28,
                 from /home/fortandh/workspace/jdk12/src/hotspot/share/classfile/classLoaderData.hpp:28,
                 from /home/fortandh/workspace/jdk12/src/hotspot/share/precompiled/precompiled.hpp:34:
/home/fortandh/workspace/jdk12/src/hotspot/share/utilities/globalDefinitions.hpp:175:7: note: ‘class HeapWord’ declared here
  175 | class HeapWord {
      |       ^~~~~~~~
/home/fortandh/workspace/jdk12/src/hotspot/share/gc/parallel/objectStartArray.cpp: In member function ‘void ObjectStartArray::reset()’:
/home/fortandh/workspace/jdk12/src/hotspot/share/gc/parallel/objectStartArray.cpp:124:73: error: ‘void* memset(void*, int, size_t)’ writing to an object of type ‘class HeapWord’ with ‘private’ member ‘HeapWord::i’ [-Werror=class-memaccess]
  124 |   memset(_blocks_region.start(), clean_block, _blocks_region.byte_size());
      |                                                                         ^
In file included from /home/fortandh/workspace/jdk12/src/hotspot/share/utilities/align.hpp:28,
                 from /home/fortandh/workspace/jdk12/src/hotspot/share/runtime/globals.hpp:29,
                 from /home/fortandh/workspace/jdk12/src/hotspot/share/memory/allocation.hpp:28,
                 from /home/fortandh/workspace/jdk12/src/hotspot/share/classfile/classLoaderData.hpp:28,
                 from /home/fortandh/workspace/jdk12/src/hotspot/share/precompiled/precompiled.hpp:34:
/home/fortandh/workspace/jdk12/src/hotspot/share/utilities/globalDefinitions.hpp:175:7: note: ‘class HeapWord’ declared here
  175 | class HeapWord {
      |       ^~~~~~~~
cc1plus: all warnings being treated as errors
```

## 原因分析
[HeapWord should not be a fake class](https://bugs.openjdk.java.net/browse/JDK-8214363)

## 解决方案
[53792:6fb43030a1b4](http://hg.openjdk.java.net/jdk/jdk/rev/6fb43030a1b4)

# strncpy() strncate() 长度考虑问题
## 问题描述
```shell
In function ‘char* strncpy(char*, const char*, size_t)’,
    inlined from ‘static int os::create_file_for_heap(const char*)’ at /home/fortandh/workspace/jdk12/src/hotspot/os/posix/os_posix.cpp:188:16:
/usr/include/x86_64-linux-gnu/bits/string_fortified.h:106:34: error: ‘char* __builtin_strncpy(char*, const char*, long unsigned int)’ specified bound depends on the length of the source argument [-Werror=stringop-overflow=]
  106 |   return __builtin___strncpy_chk (__dest, __src, __len, __bos (__dest));
      |          ~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/fortandh/workspace/jdk12/src/hotspot/os/posix/os_posix.cpp: In static member function ‘static int os::create_file_for_heap(const char*)’:
/home/fortandh/workspace/jdk12/src/hotspot/os/posix/os_posix.cpp:188:38: note: length computed here
  188 |   (void)strncpy(fullname, dir, strlen(dir)+1);
      |                                ~~~~~~^~~~~
In file included from /usr/include/string.h:495,
                 from /home/fortandh/workspace/jdk12/src/hotspot/share/utilities/globalDefinitions_gcc.hpp:35,
                 from /home/fortandh/workspace/jdk12/src/hotspot/share/utilities/globalDefinitions.hpp:32,
                 from /home/fortandh/workspace/jdk12/src/hotspot/share/utilities/align.hpp:28,
                 from /home/fortandh/workspace/jdk12/src/hotspot/share/runtime/globals.hpp:29,
                 from /home/fortandh/workspace/jdk12/src/hotspot/share/memory/allocation.hpp:28,
                 from /home/fortandh/workspace/jdk12/src/hotspot/share/logging/logLevel.hpp:27,
                 from /home/fortandh/workspace/jdk12/src/hotspot/share/logging/log.hpp:27,
                 from /home/fortandh/workspace/jdk12/src/hotspot/os/posix/os_posix.cpp:26:
In function ‘char* strncat(char*, const char*, size_t)’,
    inlined from ‘static int os::create_file_for_heap(const char*)’ at /home/fortandh/workspace/jdk12/src/hotspot/os/posix/os_posix.cpp:189:16:
/usr/include/x86_64-linux-gnu/bits/string_fortified.h:136:34: error: ‘char* __builtin_strncat(char*, const char*, long unsigned int)’ specified bound depends on the length of the source argument [-Werror=stringop-overflow=]
  136 |   return __builtin___strncat_chk (__dest, __src, __len, __bos (__dest));
      |          ~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/home/fortandh/workspace/jdk12/src/hotspot/os/posix/os_posix.cpp:189:16: note: length computed here
  189 |   (void)strncat(fullname, name_template, strlen(name_template));
      |         ~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
cc1plus: all warnings being treated as errors
```

## 原因分析
出错的代码如下：
```c++
(void)strncpy(fullname, dir, strlen(dir)+1);
(void)strncat(fullname, name_template, strlen(name_template));
```
尽量少使用strncpy函数与strncat函数，尽量用memcpy函数和snprintf函数

## 解决方案
```c++
const char name_template[] = "/jvmheap.XXXXXX";

// char *fullname = (char*)os::malloc((strlen(dir) + strlen(name_template) + 1), mtInternal);
size_t fullname_len = strlen(dir) + strlen(name_template);
char *fullname = (char*)os::malloc(fullname_len + 1, mtInternal);
if (fullname == NULL) {
    vm_exit_during_initialization(err_msg("Malloc failed during creation of backing file for heap (%s)", os::strerror(errno)));
    return -1;
}
// (void)strncpy(fullname, dir, strlen(dir)+1);
// (void)strncat(fullname, name_template, strlen(name_template));
int n = snprintf(fullname, fullname_len + 1, "%s%s", dir, name_template);
assert((size_t)n == fullname_len, "Unexpected number of characters in string");

os::native_path(fullname);
```

# 强转类型问题
## 问题描述
```shell
/home/fortandh/workspace/jdk12/src/java.base/unix/native/libjli/java_md_solinux.c: In function ‘ContinueInNewThread0’:
/home/fortandh/workspace/jdk12/src/java.base/unix/native/libjli/java_md_solinux.c:747:37: error: cast between incompatible function types from ‘int (*)(void *)’ to ‘void * (*)(void *)’ [-Werror=cast-function-type]
  747 |     if (pthread_create(&tid, &attr, (void *(*)(void*))continuation, (void*)args) == 0) {
      |                                     ^
cc1: all warnings being treated as errors
```

## 原因分析
[GCC 8 compilation error in libjli](https://bugs.openjdk.java.net/browse/JDK-8215009)

## 解决方案
[53941:f9302cf718c9](http://hg.openjdk.java.net/jdk/jdk/rev/f9302cf718c9)

## 问题描述
```shell
/home/fortandh/workspace/jdk12/src/java.desktop/share/native/liblcms/cmsxform.c: In function ‘AllocEmptyTransform’:
/home/fortandh/workspace/jdk12/src/java.desktop/share/native/liblcms/cmsxform.c:839:50: error: cast between incompatible function types from ‘_cmsTransform2Fn’ {aka ‘void (*)(struct _cmstransform_struct *, const void *, void *, unsigned int,  unsigned int,  const struct <anonymous> *)’} to ‘void (*)(struct _cmstransform_struct *, const void *, void *, cmsUInt32Number,  cmsUInt32Number)’ {aka ‘void (*)(struct _cmstransform_struct *, const void *, void *, unsigned int,  unsigned int)’} [-Werror=cast-function-type]
  839 |                                    p->OldXform = (_cmsTransformFn) p->xform;
      |                                                  ^
cc1: all warnings being treated as errors
```

## 原因分析
[Fix errors in LittleCMS 2.9 reported by GCC 8](https://bugs.openjdk.java.net/browse/JDK-8215130)

## 解决方案
[53797:c1af89d9d44c](http://hg.openjdk.java.net/jdk/jdk/rev/c1af89d9d44c)

# strncpy()参数使用常数宏的问题
## 问题描述
```shell
In function ‘strncpy’,
    inlined from ‘cmsIT8SetSheetType’ at /home/fortandh/workspace/jdk12/src/java.desktop/share/native/liblcms/cmscgats.c:1378:9,
    inlined from ‘ParseIT8’ at /home/fortandh/workspace/jdk12/src/java.desktop/share/native/liblcms/cmscgats.c:2152:41:
/usr/include/x86_64-linux-gnu/bits/string_fortified.h:106:10: error: ‘__builtin_strncpy’ output may be truncated copying 1023 bytes from a string of length 1023 [-Werror=stringop-truncation]
  106 |   return __builtin___strncpy_chk (__dest, __src, __len, __bos (__dest));
      |          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
cc1: all warnings being treated as errors
```

## 原因分析
出错的代码如下：
```c++
strncpy(t ->SheetType, Type, MAXSTR-1);
```

MAXSTR是一个宏，值为1024，因此这里警告是从长度为1023的字符串里面复制1023个字节，可能发生截断问题。

## 解决方案
```c++
// strncpy(t ->SheetType, Type, MAXSTR-1);
memcpy(t ->SheetType, Type, strnlen(Type, sizeof(t->SheetType)-1));
```

**Note：后续还有类似问题，将不再列出**

# 函数缺失属性问题
## 问题描述
```shell
/home/fortandh/workspace/jdk12/src/java.desktop/share/native/libfontmanager/harfbuzz/hb-debug.hh:149:1: note: missing primary template attribute ‘format’
  149 | _hb_debug_msg (const char *what,
      | ^~~~~~~~~~~~~
cc1plus: all warnings being treated as errors
```

## 原因分析
出错的代码如下：
```c++
template <int max_level> static inline void
_hb_debug_msg (const char *what,
               const void *obj,
               const char *func,
               bool indented,
               unsigned int level,
               int level_dir,
               const char *message,
               ...) HB_PRINTF_FUNC(7, 8);
template <int max_level> static inline void
_hb_debug_msg (const char *what,
               const void *obj,
               const char *func,
```

在该函数的检查规则中，继续添加HB_PRINTF_FUNC(7,8)

## 解决方案
```c++
template <int max_level> static inline void
_hb_debug_msg (const char *what,
               const void *obj,
               const char *func,
               bool indented,
               unsigned int level,
               int level_dir,
               const char *message,
               ...) HB_PRINTF_FUNC(7, 8);
// template <int max_level> static inline void
template <int max_level> static inline void HB_PRINTF_FUNC(7, 8)
_hb_debug_msg (const char *what,
               const void *obj,
               const char *func,
```

**Notes:后面发现，还有其他未知错误，因此使用官方fix bug 的方式：**
**[Upgrade HarfBuzz to the latest 2.3.1](http://hg.openjdk.java.net/jdk/jdk/rev/7c11a7cc7c1d)**

# snprintf()截断字符串问题
## 问题描述
```shell
/home/fortandh/workspace/jdk12/src/jdk.jdwp.agent/share/native/libjdwp/log_messages.c: In function ‘log_message_end’:
/home/fortandh/workspace/jdk12/src/jdk.jdwp.agent/share/native/libjdwp/log_messages.c:75:24: error: ‘%.3d’ directive output may be truncated writing between 3 and 11 bytes into a region of size between 0 and 80 [-Werror=format-truncation=]
   75 |                    "%s.%.3d %s", timestamp_prefix,
      |                        ^~~~
/home/fortandh/workspace/jdk12/src/jdk.jdwp.agent/share/native/libjdwp/log_messages.c:75:20: note: using the range [-2147483648, 2147483647] for directive argument
   75 |                    "%s.%.3d %s", timestamp_prefix,
      |                    ^~~~~~~~~~~~
In file included from /usr/include/stdio.h:867,
                 from /home/fortandh/workspace/jdk12/src/jdk.jdwp.agent/share/native/libjdwp/util.h:30,
                 from /home/fortandh/workspace/jdk12/src/jdk.jdwp.agent/share/native/libjdwp/log_messages.c:26:
/usr/include/x86_64-linux-gnu/bits/stdio2.h:67:10: note: ‘__builtin___snprintf_chk’ output between 6 and 174 bytes into a destination of size 81
   67 |   return __builtin___snprintf_chk (__s, __n, __USE_FORTIFY_LEVEL - 1,
      |          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   68 |        __bos (__s), __fmt, __va_arg_pack ());
      |        ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
cc1: all warnings being treated as errors
```

## 原因分析
[JDWP: Unforseen output truncation in logging](https://bugs.openjdk.java.net/browse/JDK-8214854)

## 解决方案
[53979:70c7c4db9c9a](http://hg.openjdk.java.net/jdk/jdk/rev/70c7c4db9c9a)
