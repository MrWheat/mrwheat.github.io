title: "iOS开发-Framework和XCFramework制作"

date: 2020-11-26 11:26:59

description: 

categories: iOS开发 - 基础

tags: [iOS,开发,静态库]

---

### Framework

Framework可以理解为是一个封装了共享资源的具有层次结构的文件夹。同时它也是个Bundle，可以通过Bundle的相关API来访问。

#### 制作Framework

Xcode Version: 12.0.1 

点击`Xcode -> File -> New -> Target`，选择`iOS`滑动到底部，在`Framework & Library`一栏下选择`Framework`。

#### Mach-O Type设置

Mach-O Type分为以下五种类型，一般常见的有`Dynamic Library`和`Static Library`，这里我们设置为`Static Library`。

| 参数名 | 含义 | 
| -------- | -------- |
| Executable | 可执行二进制文件 |
| Dynamic Library | 动态库 |
| Bundle | 非独立二进制文件，显式加载 |
| Static Library | 静态库 |
| Relocatable Object File | 可重定位的目标文件，中间结果 |

<!--more-->

##### 静态库(Static Libraries)

![](/img/iOS基础-Framework和XCFramework制作/20201126161901.jpg)

静态库是我们使用最常见的方式，主要以.a、.lib、.framework、.xcframework形式存在，平时用到的三方SDK基本上都是这种，它有以下几个特点：

1. 在项目编译过程中就需要导入到目标程序中，因此会一定程度上增加包的体积，当库有修改时，也必须重新编译和发布。
2. App启动时就会载入内存，因此在使用时不需要外部加载，速度快，但相应的也增加了App启动时间。
3. 减少耦合性，静态库中是不可以包含其他静态库的，使用的时候要另外导入它的依赖库，可以最大限度的保证了每一个静态库都是独立的，不会重复引用。
4. 减少对外界的依赖，如果使用第三方动态库，库找不到的话程序会崩溃。

##### 动态库(Dynamic Libraries)

![](/img/iOS基础-Framework和XCFramework制作/20201126161917.jpg)

动态库我们使用最多的就是UIkit.framework和Fundation.framework，动态库主要以.dylib、.so、.dll、.tbd、.framework、.xcframewor的形式存在，它有以下几个特点：

1. App按需加载，加速App的启动。
2. 不需要拷贝到可执行文件中，减少包的体积。
3. 维护和更新比较方便，只要接口不变，依赖动态库的App就不用重新编译。
4. 在制作上可以直接包含静态库，因此在使用时不需要再次导入其他的依赖库。

在 iOS 8 之前，iOS 平台不支持开发者使用用户自己的动态Framework，这种限制可能是出于安全的考虑。因为 iOS 应用都是运行在沙盒当中，不同的程序之间不能共享代码，同时动态下载代码又是被苹果明令禁止的，没办法发挥出动态库的优势，实际上动态库也就没有存在的必要了。

但是，iOS 8/Xcode 6 推出之后，因为Extension的出现，iOS添加了对动态库的支持，Extension 和 App 是两个分开的可执行文件，同时需要共享代码，这种情况下动态库的支持就是必不可少的了。

不过这种动态 Framework 和系统的 UIKit.Framework 还是有很大区别，系统的 Framework 不需要拷贝到目标程序中，我们自己做出来的 Framework 哪怕是动态的，最后也还是要拷贝到 App 中（App和Extension的 Bundle是共享的），因此苹果又把这种 Framework 称为 Embedded Framework。

在导入自己制作的动态库时，需要在Embedded Binaries中导入，不然会报image not found，程序崩溃。

#### 其他相关设置

`Architectures:`指定工程将被编译成支持哪些指令集，一般不包含armv7s，如果需要可以额外添加。

`Valid Architecures:`指定可能支持的指令集，该列表和Architectures列表的交集，将是Xcode最终生成的二进制包所支持的指令集。

`Build Active Architecture Only:`编译时是否只保留对应设备的指令集。

`Enable Bitcode:`是否支持Bitcode。

`Dead Code Stripping:`是否消除无效代码，设置为NO关闭对代码中dead，unreachable代码过滤。

`Link With Standard Libraries:`是否用标准库链接，设置为 NO 避免重复链接。

#### 编写代码

这里不再赘述，对于OC而言，需要暴露的头文件需要添加到 `Build Phases -> Headers -> Public` 里。

如果是Swift Framework，需要暴露的方法使用 `public` 和 `@objc` 修饰，对于那些需要继承重写的类使用 `open` 修饰。

#### 创建通用包

编译过后，会在`Products`生成一个包，如果是真机则是真机包，如果是模拟器则是模拟器包。这两个包的区别是包含的指令集不一致。

真机：armv7｜armv7s｜arm64
模拟器：i386｜x86_64

在终端通过lipo指令查看包的指令集。

> lipo -info xxxx/ProjectName.framework/ProjcetName

不同的包只能在固定的场景下使用，为了省去来回切换的麻烦，我们需要将两个包进行合并。

> lipo -create PATH1 PATH2 -output PATH3

这一步是为了合并两者的二进制文件`ProjectName.framework/ProjcetName`，PATH1、PATH2和PATH3均为二进制文件路径，合并完成之后将真机或者模拟器的二进制文件替换为合并之后的。

基本到这里就结束了，但是对于Swfit来讲，仅仅合并二进制文件是不够的，还需要合并`ProjectName.framework/Modules/`文件夹和`swift header file`，所以建议还是采用脚本的方式。

点击`Xcode -> File -> New -> Target`，选择`Other`，创建`Aggregate`，在新建的Aggregate下点击`+`号添加`New Run Script Phase`，添加以下脚本：

```
# Set bash script to exit immediately if any commands fail.
set -e
# Setup some constants for use later on.
FRAMEWORK_NAME=${PROJECT_NAME}
OUTPUT_DIR="${SRCROOT}/build"

# If remains from a previous build exist,delete them.
if [ -d "${OUTPUT_DIR}" ]; then
rm -rf "${OUTPUT_DIR}"
fi

# Build the framework for device and for simulator.
echo "Building for device"
xcodebuild -project "${FRAMEWORK_NAME}.xcodeproj" -scheme "${FRAMEWORK_NAME}" -configuration Release -arch arm64 -arch armv7 only_active_arch=no defines_module=yes -sdk "iphoneos" -derivedDataPath "${OUTPUT_DIR}"
echo "Building for Simulator"
xcodebuild -project "${FRAMEWORK_NAME}.xcodeproj" -scheme "${FRAMEWORK_NAME}" -configuration Release -arch x86_64 -arch i386 only_active_arch=no defines_module=yes -sdk "iphonesimulator" -derivedDataPath "${OUTPUT_DIR}"

# Remove .framework file if exists from previous run.
if [ -d "${OUTPUT_DIR}/${FRAMEWORK_NAME}.framework" ]; then
rm -rf "${OUTPUT_DIR}/${FRAMEWORK_NAME}.framework"
fi

# Copy the device version of framework.
cp -r "${OUTPUT_DIR}/Build/Products/Release-iphoneos/${FRAMEWORK_NAME}.framework" "${OUTPUT_DIR}/${FRAMEWORK_NAME}.framework"

# Merging the device and simulator frameworks' executables with lipo.
lipo -create -output "${OUTPUT_DIR}/${FRAMEWORK_NAME}.framework/${FRAMEWORK_NAME}" "${OUTPUT_DIR}/Build/Products/Release-iphoneos/${FRAMEWORK_NAME}.framework/${FRAMEWORK_NAME}" "${OUTPUT_DIR}/Build/Products/Release-iphonesimulator/${FRAMEWORK_NAME}.framework/${FRAMEWORK_NAME}"

# Copy Swift module mappings for simulator into the framework. 
cp -r "${OUTPUT_DIR}/Build/Products/Release-iphonesimulator/${FRAMEWORK_NAME}.framework/Modules/${FRAMEWORK_NAME}.swiftmodule/" "${OUTPUT_DIR}/${FRAMEWORK_NAME}.framework/Modules/${FRAMEWORK_NAME}.swiftmodule"
cp -r "${OUTPUT_DIR}/Build/Products/Release-iphoneos/${FRAMEWORK_NAME}.framework/Modules/${FRAMEWORK_NAME}.swiftmodule/" "${OUTPUT_DIR}/${FRAMEWORK_NAME}.framework/Modules/${FRAMEWORK_NAME}.swiftmodule"

# Create new combined simulator and device swift header file.
COMBINED_PATH="${BUILD_DIR}/iOS + iOS Simulator/${PROJECT_NAME}-Swift.h"
mkdir -p "${BUILD_DIR}/iOS + iOS Simulator/"
touch "${COMBINED_PATH}"
echo "#ifndef TARGET_OS_SIMULATOR\n#include <TargetConditionals.h>\n#endif\n#if TARGET_OS_SIMULATOR" >> "${COMBINED_PATH}"
cat "${OUTPUT_DIR}/Build/Products/Release-iphonesimulator/${FRAMEWORK_NAME}.framework/Headers/${FRAMEWORK_NAME}-Swift.h" >> "${COMBINED_PATH}"
echo "#else" >> "${COMBINED_PATH}"
echo "//Start of iphoneos" >> "${COMBINED_PATH}"
cat "${OUTPUT_DIR}/Build/Products/Release-iphoneos/${FRAMEWORK_NAME}.framework/Headers/${FRAMEWORK_NAME}-Swift.h" >> "${COMBINED_PATH}"
echo "#endif" >> "${COMBINED_PATH}"

# Overwrite generated -Swift.h file with combined -Swift.h file 
cat "$COMBINED_PATH" > "${OUTPUT_DIR}/${FRAMEWORK_NAME}.framework/Headers/${FRAMEWORK_NAME}-Swift.h"

#Optional Step to copy the framework to root folder
cp -r "${OUTPUT_DIR}/${FRAMEWORK_NAME}.framework" "${SRCROOT}"
```

打包上线时删除未使用的指令集：

```
APP_PATH="${TARGET_BUILD_DIR}/${WRAPPER_NAME}"
# This script loops through the frameworks embedded in the application and
# removes unused architectures.
find "$APP_PATH" -name '*.framework' -type d | whileread -r FRAMEWORK
do
FRAMEWORK_EXECUTABLE_NAME=$(defaults read"$FRAMEWORK/Info.plist" CFBundleExecutable)
FRAMEWORK_EXECUTABLE_PATH="$FRAMEWORK/$FRAMEWORK_EXECUTABLE_NAME"
echo "Executable is $FRAMEWORK_EXECUTABLE_PATH"
EXTRACTED_ARCHS=()
GOOD_ARCHS=()
PRESENT_ARCHS=($(lipo -archs "$FRAMEWORK_EXECUTABLE_PATH"))
if [[ "${#PRESENT_ARCHS[@]}" -lt 2 ]]
then
   echo "Framework is not a Fat binary, skipping..."
   continue
fi
for ARCH in "${PRESENT_ARCHS[@]}"
do
if [[ "$ARCH" != x86_64 && "$ARCH" != i386 ]]
then
   echo "Extracting $ARCH from $FRAMEWORK_EXECUTABLE_NAME"
   lipo -extract "$ARCH" "$FRAMEWORK_EXECUTABLE_PATH" -o "$FRAMEWORK_EXECUTABLE_PATH-$ARCH"
   GOOD_ARCHS+=("$ARCH")
   EXTRACTED_ARCHS+=("$FRAMEWORK_EXECUTABLE_PATH-$ARCH")
   fi
done
echo "Merging extracted architectures: ${ARCHS}"
lipo -o "$FRAMEWORK_EXECUTABLE_PATH-merged" -create "${EXTRACTED_ARCHS[@]}"
rm "${EXTRACTED_ARCHS[@]}"
echo "Replacing original executable with thinned version"
rm "$FRAMEWORK_EXECUTABLE_PATH"
mv "$FRAMEWORK_EXECUTABLE_PATH-merged""$FRAMEWORK_EXECUTABLE_PATH"
done
```

### XCFramework

XCFramework 是 Xcode 11 引入的，一个可分发的二进制包，它包含了 framework 的一个或多个变体，XCFramework的好处就是用 Xcode 发布的时候，Xcode 会自动选用正确的指令集Framework，省去了手动移除动态库中的模拟器指令集的工作。

设置方式和Framework一致，可以是静态的也可以是动态的，打包XCFramework的脚本如下：

```
#!/bin/sh -e

REVEAL_XCFRAMEWORK_IN_FINDER=true

FREAMEWORK_NAME="${PROJECT_NAME}"
FREAMEWORK_OUTPUT_DIR="${PROJECT_DIR}/Distribution"
ARCHIVE_PATH_IOS_DEVICE="./Build/ios_device.xcarchive"
ARCHIVE_PATH_IOS_SIMULATOR="./Build/ios_simulator.xcarchive"
# ARCHIVE_PATH_MACOS="./build/macos.xcarchive"

function archiveOnePlatform {
    echo "▸ Starts archiving the scheme: ${1} for destination: ${2};\n▸ Archive path: ${3}"

    xcodebuild archive \
        -scheme "${1}" \
        -destination "${2}" \
        -archivePath "${3}" \
        VALID_ARCHS="${4}" \
        SKIP_INSTALL=NO \
        BUILD_LIBRARY_FOR_DISTRIBUTION=YES | xcpretty

    # sudo gem install -n /usr/local/bin xcpretty
    # xcpretty makes xcode compile information much more readable.
}

function archiveAllPlatforms {

    # https://www.mokacoding.com/blog/xcodebuild-destination-options/

    # Platform                Destination
    # iOS                    generic/platform=iOS
    # iOS Simulator            generic/platform=iOS Simulator
    # iPadOS                generic/platform=iPadOS
    # iPadOS Simulator        generic/platform=iPadOS Simulator
    # macOS                    generic/platform=macOS
    # tvOS                    generic/platform=tvOS
    # watchOS                generic/platform=watchOS
    # watchOS Simulator        generic/platform=watchOS Simulator
    # carPlayOS                generic/platform=carPlayOS
    # carPlayOS Simulator    generic/platform=carPlayOS Simulator

    SCHEME=${1}

    archiveOnePlatform $SCHEME "generic/platform=iOS Simulator" ${ARCHIVE_PATH_IOS_SIMULATOR} "x86_64"
      archiveOnePlatform $SCHEME "generic/platform=iOS" ${ARCHIVE_PATH_IOS_DEVICE} "armv7 arm64"
      # archiveOnePlatform $SCHEME "generic/platform=macOS" ${ARCHIVE_PATH_MACOS}
}

function makeXCFramework {

    FRAMEWORK_RELATIVE_PATH="Products/Library/Frameworks"
    OUTPUT_DIR="${FREAMEWORK_OUTPUT_DIR}/DynamicFramework"

    mkdir -p "${OUTPUT_DIR}"

    xcodebuild -create-xcframework \
        -framework "${ARCHIVE_PATH_IOS_DEVICE}/${FRAMEWORK_RELATIVE_PATH}/${FREAMEWORK_NAME}.framework" \
        -framework "${ARCHIVE_PATH_IOS_SIMULATOR}/${FRAMEWORK_RELATIVE_PATH}/${FREAMEWORK_NAME}.framework" \
        -output "${OUTPUT_DIR}/${FREAMEWORK_NAME}.xcframework"
}

echo "#####################"
echo "▸ Cleaning XCFramework output dir: ${FREAMEWORK_OUTPUT_DIR}"
rm -rf $FREAMEWORK_OUTPUT_DIR

#### Make XCFramework

echo "▸ Archive framework: ${FREAMEWORK_NAME}"
archiveAllPlatforms $FREAMEWORK_NAME

echo "▸ Make framework: ${FREAMEWORK_NAME}.xcframework"
makeXCFramework

# Clean Build
rm -rf "./Build"

if [ ${REVEAL_XCFRAMEWORK_IN_FINDER} = true ]; then
    open "${FREAMEWORK_OUTPUT_DIR}/"
fi

```

### 常见错误

#### Category错误

错误描述：静态库中如果包含了Category（分类），有时候在使用静态库的工程中会报“方法找不到”的错误（unrecognized selector sent to instance）。

具体原因：参见[编译参数-objc说明](http://www.tuicool.com/articles/qQzeia)

解决方案：在使用静态库的工程中配置`Other Linker Flags`为-ObjC
	
#### 制作成动态库

错误描述：在使用静态库时，运行报错(Reason: Image Not Found)

具体原因：可能由于没有设置`Mach-O Type`，做的是动态库，在使用的时候需要额外加一个步骤，要把Framework同时添加到`General --> Embedded Binaries`中。

解决方案：将`Mach-O Type`设置为`Static Library`
	
#### OC项目引用Swift Framework

**错误日志：**
	
```
Undefined symbol: __swift_FORCE_LOAD_$_swiftCompatibilityDynamicReplacements
Undefined symbol: __swift_FORCE_LOAD_$_swiftCompatibility51
Undefined symbol: __swift_FORCE_LOAD_$_swiftCompatibility50
```
	
**解决方式：**在项目的`Build Setting -> LIBRARY_SEARCH_PATHS`添加
    
```
$(TOOLCHAIN_DIR)/usr/lib/swift/$(PLATFORM_NAME)
$(TOOLCHAIN_DIR)/usr/lib/swift-5.0/$(PLATFORM_NAME)
```
    
**错误日志：**
    
![](/img/iOS基础-Framework和XCFramework制作/A1110831-7D75-454A-97B7-E7872615708A.jpg)
    
**解决方式：**修改项目配置`Build Setting -> ALWAYS_EMBED_SWIFT_STANDARD_LIBRARIES`为YES
    
**错误日志：**
    
![](/img/iOS基础-Framework和XCFramework制作/2AE04CC8-3C7B-4D2D-88C5-4F32DDC98DEC.jpg)
    
**解决方式：**在项目的`Build Setting -> LD_RUNPATH_SEARCH_PATHS`添加（一定要加在第一行）

```
/usr/lib/swift
```
    
如果某些系统库找不到，则在项目的`Build Phases -> Link Binary With Libraries`添加Swift依赖库：
    
```
libswiftCompression.tbd
libswiftUIKit.tbd
libswiftCore.tbd
```
	
### 参考
	
[Custom Universal Framework in iOS](https://medium.com/@priya_talreja/create-custom-universal-framework-in-ios-aef7fa6fd51e)

[How to build a binary framework in Xcode 11](https://www.userdesk.io/blog/how-to-build-a-binary-framework-in-xcode-11/)
