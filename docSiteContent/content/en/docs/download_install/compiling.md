---
title: Compiling
linktitle: Compiling
type: book
date: "2019-05-05T00:00:00+01:00"
# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
weight: 4
---

Now that everything is ready, it is time to get into the compiling step proper.

1. Use `cd ..` to go back to the `FMT-Installation` folder.

2. Clone the repository of FMT by using `git clone https://github.com/Bureau-du-Forestier-en-chef/FMT.git`

3. Use `cd ./FMT` to navigate into the FMT folder downloaded from the repository of FMT.

4. Copy/paste the following commands **in the Windows notepad**, and then replace the `MOSEK_DIR`,`CMAKE_TOOLCHAIN_FILE` and `PathTovcvars64` with the location of Mosek, vcpkg.cmake in vcpkg and vcvars64 on your computer:

```bash
call "PathTovcvars64\vcvars64.bat"
set MOSEK_DIR=MyPathToMosek
set MODULE_PATH=%cd%/Modules
set "MODULE_PATH=%MODULE_PATH:\=/%"
set VCPKG_KEEP_ENV_VARS=MOSEK_DIR;MODULE_PATH

cmake CMakeLists.txt -B build/release -G "Visual Studio 17 2022" -DBUILD_TYPE=Release -T v143,version=14.36.17.6 -DPARALLEL_TESTS=8 -DCMAKE_TOOLCHAIN_FILE="MyPathTovcpkg.cmake" -DVCPKG_TARGET_TRIPLET=x64-windows -DVCPKG_MANIFEST_MODE=ON

cmake --build build/release --config Release
cmake --install build/release --config Release
```

{{% callout note %}}
By default, Mosek was installed in your `C:/Program Files` folder on windows. This can be expressed by the path `C:/PROGRA~1` to avoid the space between `Program` and `Files`, which can create errors.

Thus, if you installed Mosek in `C:\Program Files\Mosek`, you can keep `C:/PROGRA~1/Mosek/` in the commands.
{{% /callout %}}

{{% callout note %}}
If you installed a version of Visual Studio anterior to 2022, you'll have to edit the part of the commands incating the version of Visual Studio. 
{{% /callout %}}

5. Copy/paste all of the commands that are ready in the notepad into the command prompt to start the compilation. Press enter to activate them.

{{% callout warning %}}
This will take time, and you might see warning in the command prompt during the process. Don't worry about them.
{{% /callout %}}

{{% callout warning %}}
If the compilation ends up with error messages that are red, you can come and [post an issue on the issue page of the repository for FMT](https://github.com/gcyr/FMT/issues).
{{% /callout %}}

* * *

If everything worked, congratulations ! Now, it leaves us with importing FMT in Python, and seeing that everything works.