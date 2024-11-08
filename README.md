# add_DivExpression2IR

This is my assignment
for a function evaluating the expression "(a + b) / x", where "a" and "b" are 32-bit integers and "x" is a float.

Nov 07, 2024 YunzhiLi

### 1. About C compile errors and why CPP was involved.
```
The story starts from the errors:
{
...
// Print the generated IR to stdout
char *ir_string = LLVMPPrintModuleToString(module);
printf("%s\n", ir_string);
...
}
```
I encountered below error on my first trying to implement the C PGM for generating the IR.
/usr/bin/ld: /tmp/assignment-c9a21b.o:assignment.c:(.text+0x174): undefined reference to `LLVMPPrintModuleToString'
clang-8: error: linker command failed with exit code 1 (use -v to see invocation)

With the google's result:
The function LLVMPPrintModuleToString is part of the LLVM C API, specifically used for printing the textual representation of an LLVM module.
However, it appears that this function is not included in the standard LLVM libraries in more recent versions.
Instead, you can achieve similar functionality using the llvm::Module::print method, which is part of the C++ API. I struggled a lot and could not fix it quickly. So I try to rewrite it with CPP and also followed the c-coding style. I sorry for bring the inconvinence to you/team.
### 2. Compile environment:
Visual 2022 Community X64

```
George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ clang++ -v
clang version 8.0.1 (tags/RELEASE_801/final)
Target: x86_64-unknown-windows-cygnus
Thread model: posix
InstalledDir: /usr/bin 

$George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src$ clang -v
clang version 8.0.1 (tags/RELEASE_801/final)
Target: x86_64-unknown-windows-cygnus
Thread model: posix
InstalledDir: /usr/bin
```

### 3. How to compile:
1). Open VS2022 X64 Native Tools Command Prompt for VS2022
2). > C:\cygwin\Cygwin.bat to open the cygwin bash.
3). $ cd d:
4). cd to the soure path(eg:/cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src)
5). To build the Cpp PGM which is used for generating the IR code.

```
$ clang++ -v -g -O3 -o generateFun.exe generateFun.cpp -std=c++17 `llvm-config --ldflags --system-libs --libs core`
```

### 4. output the IR code(The generated IR code will be saved in fun.ll;
will then use cmd `llvm-as fun.ll -o fun.bc` to generate the corresponding fun.bc):

```
George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ ./generateFun
; ModuleID = 'fun.c'
source_filename = "fun.c"define float @fun(i32 %a, i32 %b, float %x) {
entry:
%ifcond = fcmp one float %x, 0.000000e+00
br i1 %ifcond, label %if_then, label %if_elseif_then:                                          ; preds = %entry
%sum = add i32 %a, %b
%fsum = sitofp i32 %sum to float
%result = fdiv float %fsum, %x
ret float %resultif_else:                                          ; preds = %entry
ret float %x
}
```

### 5. Execute results:

  ```
  George.LeeYZ@YunzhiLi MINGW64 /d/02_WorkSpace/001_BroadR22LLVM/01_src
  $ lli output.bc
  the (5 + 10)/ 3.00 = 5.00

  ```
### 6. Generate the .o and .lib

  ```
  George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/Yunzhi_src
  $ ./generateFun > fun.ll
  George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/Yunzhi_src
  $ llc -filetype=obj fun.ll -o fun.obj
  George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/Yunzhi_src
  $ clang -shared -o fun.dll fun.obj
  George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/Yunzhi_src
  $ clang -o yunzhi_program.exe main.c -L. -lfun
  George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/Yunzhi_src
  $ ./yunzhi_program
  the (5 + 10)/ 3.00 = -4237.67   !!!!! the error
  the (5 + 10)/ 0.00 = 0.00
  ```
### 7. Logs(for reference)

```
George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$
George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ clang++ -v -g -O3 -o generateFun.exe generateFun.cpp -std=c++17 `llvm-config --ldflags --system-libs --libs core`
clang version 8.0.1 (tags/RELEASE_801/final)
Target: x86_64-unknown-windows-cygnus
Thread model: posix
InstalledDir: /usr/bin
"/usr/bin/clang-8" -cc1 -triple x86_64-unknown-windows-cygnus -emit-obj -disable-free -disable-llvm-verifier -discard-value-names -main-file-name generateFun.cpp -mrelocation-model pic -pic-level 2 -mthread-model posix -fmath-errno -masm-verbose -mconstructor-aliases -munwind-tables -target-cpu x86-64 -dwarf-column-info -debug-info-kind=limited -dwarf-version=4 -debugger-tuning=gdb -momit-leaf-frame-pointer -v -resource-dir /usr/lib/clang/8.0.1 -internal-isystem /usr/x86_64-pc-cygwin/include/c++ -internal-isystem /usr/x86_64-pc-cygwin/include/c++/x86_64-pc-cygwin -internal-isystem /usr/x86_64-pc-cygwin/include/c++/backward -internal-isystem /usr/x86_64-pc-cygwin/include/c++/12 -internal-isystem /usr/x86_64-pc-cygwin/include/c++/12/x86_64-pc-cygwin -internal-isystem /usr/x86_64-pc-cygwin/include/c++/12/backward -internal-isystem /usr/include/c++/12 -internal-isystem /usr/include/c++/12/x86_64-pc-cygwin -internal-isystem /usr/include/c++/12/backward -internal-isystem /usr/lib/gcc/x86_64-pc-cygwin/12/include/c++ -internal-isystem /usr/lib/gcc/x86_64-pc-cygwin/12/include/c++/x86_64-pc-cygwin -internal-isystem /usr/lib/gcc/x86_64-pc-cygwin/12/include/c++/backward -internal-isystem /usr/lib/clang/8.0.1/include -internal-isystem /usr/local/include -internal-isystem /usr/lib/clang/8.0.1/include -internal-isystem /usr/x86_64-pc-cygwin/include -internal-isystem /usr/include -internal-isystem /usr/include/w32api -O3 -std=c++17 -fdeprecated-macro -fdebug-compilation-dir /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src -ferror-limit 19 -fmessage-length 183 -fobjc-runtime=gcc -fcxx-exceptions -fexceptions -fseh-exceptions -fdiagnostics-show-option -fcolor-diagnostics -vectorize-loops -vectorize-slp -o /tmp/generateFun-9a3456.o -x c++ generateFun.cpp -faddrsig
clang -cc1 version 8.0.1 based upon LLVM 8.0.1 default target x86_64-unknown-cygwin
ignoring nonexistent directory "/usr/x86_64-pc-cygwin/include/c++"
ignoring nonexistent directory "/usr/x86_64-pc-cygwin/include/c++/x86_64-pc-cygwin"
ignoring nonexistent directory "/usr/x86_64-pc-cygwin/include/c++/backward"
ignoring nonexistent directory "/usr/x86_64-pc-cygwin/include/c++/12"
ignoring nonexistent directory "/usr/x86_64-pc-cygwin/include/c++/12/x86_64-pc-cygwin"
ignoring nonexistent directory "/usr/x86_64-pc-cygwin/include/c++/12/backward"
ignoring nonexistent directory "/usr/include/c++/12"
ignoring nonexistent directory "/usr/include/c++/12/x86_64-pc-cygwin"
ignoring nonexistent directory "/usr/include/c++/12/backward"
ignoring nonexistent directory "/usr/local/include"
ignoring nonexistent directory "/usr/x86_64-pc-cygwin/include"
ignoring duplicate directory "/usr/lib/clang/8.0.1/include"
#include "..." search starts here:
#include <...> search starts here:
/usr/lib/gcc/x86_64-pc-cygwin/12/include/c++
/usr/lib/gcc/x86_64-pc-cygwin/12/include/c++/x86_64-pc-cygwin
/usr/lib/gcc/x86_64-pc-cygwin/12/include/c++/backward
/usr/lib/clang/8.0.1/include
/usr/include
/usr/include/w32api
End of search list.
"/usr/bin/ld" -m i386pep --wrap _Znwm --wrap _Znam --wrap _ZnwmRKSt9nothrow_t --wrap _ZnamRKSt9nothrow_t --wrap _ZdlPv --wrap _ZdaPv --wrap _ZdlPvRKSt9nothrow_t --wrap _ZdaPvKSt9nothrow_t -Bdynamic --tsaware -o generateFun.exe /usr/lib/crt0.o /usr/lib/gcc/x86_64-pc-cygwin/12/crtbegin.o -L/usr/lib -L/usr/lib/gcc/x86_64-pc-cygwin/12 -L/usr/x86_64-pc-cygwin/lib -L/usr/lib -L/usr/lib/w32api /tmp/generateFun-9a3456.o -lLLVMCore -lLLVMBinaryFormat -lLLVMSupport -lLLVMDemangle -lstdc++ -liconv -lgcc_s -lgcc -lcygwin -ladvapi32 -lshell32 -luser32 -lkernel32 /usr/lib/default-manifest.o /usr/lib/gcc/x86_64-pc-cygwin/12/crtend.o

George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ ls -ltr
total 1174
-rwxrwx---+ 1 George.LeeYZ None    3523 Nov  7 22:45 generateFun.cpp
-rwxrwx---+ 1 George.LeeYZ None     128 Nov  7 23:09 fun.c
-rwxrwx---+ 1 George.LeeYZ None     278 Nov  7 23:10 main.c
-rw-rw-r--+ 1 George.LeeYZ None    2544 Nov  7 23:10 main.ll
-rw-rw-r--+ 1 George.LeeYZ None    2884 Nov  7 23:10 main.bc
-rw-rw-r--+ 1 George.LeeYZ None     457 Nov  7 23:12 fun.ll
-rw-rw-r--+ 1 George.LeeYZ None     996 Nov  7 23:13 fun.bc
-rwxrwxr-x+ 1 George.LeeYZ None 1177205 Nov  7 23:16 generateFun.exe

George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ ./generateFun
; ModuleID = 'fun.c'
source_filename = "fun.c"

define float @fun(i32 %a, i32 %b, float %x) {
entry:
%ifcond = fcmp one float %x, 0.000000e+00
br i1 %ifcond, label %if_then, label %if_else

if_then:                                          ; preds = %entry
%sum = add i32 %a, %b
%fsum = sitofp i32 %sum to float
%result = fdiv float %fsum, %x
ret float %result

if_else:                                          ; preds = %entry
ret float %x
}

George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ cat main.c
// main.c
#include <stdio.h>

extern float fun(int a, int b, float x);
int main(int argc, char* argv[])
{

int a = 5;
int b = 10;
float x = 3.0;

float ret  = fun(a, b, x);
printf("the (%d + %d)/ %.2f = %.2f\n", a, b, x, ret);

return 0;
}
George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ cat fun.c

float fun(int a, int b, float x)
{
if((float)0.0 != x)
{
return (a+b)/x;
}
return (float)0.0;
}
George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ clang -emit-llvm -S main.c -o main.ll

George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ clang -emit-llvm -S fun.c -o fun.ll

George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ cat fun.ll
; ModuleID = 'fun.c'
source_filename = "fun.c"
target datalayout = "e-m:w-i64:64-f80:128-n8:16:32:64-S128"
target triple = "x86_64-unknown-windows-cygnus"

; Function Attrs: noinline nounwind optnone uwtable
define dso_local float @fun(i32, i32, float) #0 {
%4 = alloca float, align 4
%5 = alloca i32, align 4
%6 = alloca i32, align 4
%7 = alloca float, align 4
store i32 %0, i32* %5, align 4
store i32 %1, i32* %6, align 4
store float %2, float* %7, align 4
%8 = load float, float* %7, align 4
%9 = fcmp une float 0.000000e+00, %8
br i1 %9, label %10, label %17

; <label>:10:                                     ; preds = %3
%11 = load i32, i32* %5, align 4
%12 = load i32, i32* %6, align 4
%13 = add nsw i32 %11, %12
%14 = sitofp i32 %13 to float
%15 = load float, float* %7, align 4
%16 = fdiv float %14, %15
store float %16, float* %4, align 4
br label %18

; <label>:17:                                     ; preds = %3
store float 0.000000e+00, float* %4, align 4
br label %18

; <label>:18:                                     ; preds = %17, %10
%19 = load float, float* %4, align 4
ret float %19
}

attributes #0 = { noinline nounwind optnone uwtable "correctly-rounded-divide-sqrt-fp-math"="false" "disable-tail-calls"="false" "less-precise-fpmad"="false" "min-legal-vector-width"="0" "no-frame-pointer-elim"="false" "no-infs-fp-math"="false" "no-jump-tables"="false" "no-nans-fp-math"="false" "no-signed-zeros-fp-math"="false" "no-trapping-math"="false" "stack-protector-buffer-size"="8" "target-cpu"="x86-64" "target-features"="+fxsr,+mmx,+sse,+sse2,+x87" "unsafe-fp-math"="false" "use-soft-float"="false" }

!llvm.module.flags = !{!0, !1}
!llvm.ident = !{!2}

!0 = !{i32 1, !"wchar_size", i32 2}
!1 = !{i32 7, !"PIC Level", i32 2}
!2 = !{!"clang version 8.0.1 (tags/RELEASE_801/final)"}

George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ ./generateFun > fun.ll

George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ cat fun.ll
; ModuleID = 'fun.c'
source_filename = "fun.c"

define float @fun(i32 %a, i32 %b, float %x) {
entry:
%ifcond = fcmp one float %x, 0.000000e+00
br i1 %ifcond, label %if_then, label %if_else

if_then:                                          ; preds = %entry
%sum = add i32 %a, %b
%fsum = sitofp i32 %sum to float
%result = fdiv float %fsum, %x
ret float %result

if_else:                                          ; preds = %entry
ret float %x
}

George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ llvm-as fun.ll -o fun.bc

George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ llvm-as main.ll -o main.bc

George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ llvm-link fun.bc main.bc -o output.bc

George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ ls -ltr
total 1178
-rwxrwx---+ 1 George.LeeYZ None    3523 Nov  7 22:45 generateFun.cpp
-rwxrwx---+ 1 George.LeeYZ None     128 Nov  7 23:09 fun.c
-rwxrwx---+ 1 George.LeeYZ None     278 Nov  7 23:10 main.c
-rwxrwxr-x+ 1 George.LeeYZ None 1177205 Nov  7 23:16 generateFun.exe
-rw-rw-r--+ 1 George.LeeYZ None    2544 Nov  7 23:18 main.ll
-rw-rw-r--+ 1 George.LeeYZ None     458 Nov  7 23:19 fun.ll
-rw-rw-r--+ 1 George.LeeYZ None     996 Nov  7 23:19 fun.bc
-rw-rw-r--+ 1 George.LeeYZ None    2884 Nov  7 23:19 main.bc
-rw-rw-r--+ 1 George.LeeYZ None    3008 Nov  7 23:20 output.bc

George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$

George.LeeYZ@YunzhiLi MINGW64 /d/02_WorkSpace/001_BroadR22LLVM/01_src
$ ls -ltr
total 1178
-rw-r--r-- 1 George.LeeYZ 197121    3523 Nov  7 22:45 generateFun.cpp
-rw-r--r-- 1 George.LeeYZ 197121     128 Nov  7 23:09 fun.c
-rw-r--r-- 1 George.LeeYZ 197121     278 Nov  7 23:10 main.c
-rwxr-xr-x 1 George.LeeYZ 197121 1177205 Nov  7 23:16 generateFun.exe*
-rw-r--r-- 1 George.LeeYZ 197121    2544 Nov  7 23:18 main.ll
-rw-r--r-- 1 George.LeeYZ 197121     458 Nov  7 23:19 fun.ll
-rw-r--r-- 1 George.LeeYZ 197121     996 Nov  7 23:19 fun.bc
-rw-r--r-- 1 George.LeeYZ 197121    2884 Nov  7 23:19 main.bc
-rw-r--r-- 1 George.LeeYZ 197121    3008 Nov  7 23:20 output.bc

George.LeeYZ@YunzhiLi MINGW64 /d/02_WorkSpace/001_BroadR22LLVM/01_src
$ lli output.bc
the (5 + 10)/ 3.00 = 5.00

George.LeeYZ@YunzhiLi MINGW64 /d/02_WorkSpace/001_BroadR22LLVM/01_src
$
George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ clang -emit-llvm -S main.c -o main.ll

George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ cat main.c
// main.c
#include <stdio.h>

extern float fun(int a, int b, float x);
int main(int argc, char* argv[])
{

int a = 5;
int b = 10;
float x = 3.0;

float ret  = fun(a, b, x);
printf("the (%d + %d)/ %.2f = %.2f\n", a, b, x, ret);

x = 0.0;

ret  = fun(a, b, x);
printf("the (%d + %d)/ %.2f = %.2f\n", a, b, x, ret);
return 0;
}
George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ clang -emit-llvm -S main.c -o main.ll

George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ llvm-as main.ll -o main.bc

George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ llvm-link fun.bc main.bc -o output.bc

George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$ C:/clang+llvm-18.1.8-x86_64-pc-windows-msvc/bin/lli.exe output.bc
the (5 + 10)/ 3.00 = 5.00
the (5 + 10)/ 0.00 = 0.00

George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/01_src
$

George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/Yunzhi_src
$ ./generateFun > fun.ll
George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/Yunzhi_src
$ llc -filetype=obj fun.ll -o fun.obj
George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/Yunzhi_src
$ clang -shared -o fun.dll fun.obj
George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/Yunzhi_src
$ clang -o yunzhi_program.exe main.c -L. -lfun
George.LeeYZ@YunzhiLi /cygdrive/d/02_WorkSpace/001_BroadR22LLVM/Yunzhi_src
$ ./yunzhi_program
  the (5 + 10)/ 3.00 = -4237.67   !!!!! the error
  the (5 + 10)/ 0.00 = 0.00
  
```
## End

Thanks
