title: "iOS开发本地缓存管理"

date: 2016-07-22 16:56:19

description:

categories: iOS笔记

tags: [笔记,缓存,iOS]

---

移动应用在请求网络数据时，为了提高应用性能，节省流量，一般都会做数据缓存处理。最常见的就是图片和视频缓存，目前很流行的图片处理框架SDWebImage便自带图片缓存。

对于阅读、资讯类App来说，数据缓存和清理缓存几乎是必不可少的两种功能。

关于缓存，系统提供了NSFileManager API来方便我们管理，这里介绍这两种功能的简单实现（以普通文件格式存储在沙盒目录）。

<!--more-->

###创建缓存目录

第一步当然是创建缓存文件夹，用来集中管理应用的缓存文件。

	#pragma mark -- 创建缓存文件夹
	- (void)createCacheDirectory {
	    NSString *cachePath = [NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) lastObject];
	    // 保存缓存路径
	    self.cacheDirectoryPath = [cachePath stringByAppendingPathComponent:"AppCache"];
	    NSFileManager *fm = [NSFileManager defaultManager];
	    BOOL isExist = NO;
	    // 判断文件夹是否存在
	    [fm fileExistsAtPath:self.cacheDirectoryPath isDirectory:&isExist];
	    if (!isExist) {
	    	// 如果不存在，则创建缓存文件夹 
	        [fm createDirectoryAtPath:self.cacheDirectoryPath withIntermediateDirectories:YES attributes:nil error:nil];
	    }
	}

###缓存文件

创建好缓存文件夹后，首先下载数据。

然后判断数据缓存是否存在或过期。

	#pragma mark -- 判断文件是否存在和过期
	// urlString：缓存文件名  time：超时时间
	- (BOOL)isOutTimeWithUrl:(NSString *)urlString time:(NSTimeInterval)time{
	    BOOL isTimeOut = YES;
	    // 根据文件名获取缓存地址
	    NSString *pathName = [self.cacheDirectoryPath stringByAppendingPathComponent:urlString];
	    // 判断文件是否存在
	    if ([[NSFileManager defaultManager] fileExistsAtPath: pathName]) {
	        NSFileManager *fm = [NSFileManager defaultManager];
	        NSDictionary *attribute = [fm attributesOfItemAtPath:pathName error:nil];
	        // 获取文件的创建日期
	        NSDate *date = attribute[NSFileCreationDate];
	        NSTimeInterval interval = [[NSDate date] timeIntervalSinceDate:date];
	        // 判断是否过期
	        isTimeOut = interval > time;
	    }
	    return isTimeOut;
	}
	
如果不存在或已过期，则缓存文件。

	#pragma mark -- 缓存url接口对应的数据
	- (void)cacheData:(NSData *)data forUrl:(NSString *)urlString {
	    NSString *pathName = [self.cacheDirectoryPath stringByAppendingPathComponent: urlString];
	    [data writeToFile:pathName atomically:YES];
	}
	
###计算缓存大小

计算缓存大小有两种方式，计算单个文件大小，计算所有文件大小。

计算单个文件大小。

	#pragma mark -- 计算单个文件大小
	- (float)fileSizeAtPath:(NSString *)path {
	    NSFileManager *fileManager=[NSFileManager defaultManager];
	    if([fileManager fileExistsAtPath:path]) {
	        long long size=[fileManager attributesOfItemAtPath:path error:nil].fileSize;
	        return size/1024.0/1024.0;
	    }
	    return 0;
	}

计算所有文件大小。

	#pragma mark -- 计算所有文件大小
	- (float)folderSizeAtPath:(NSString *)path {
	    NSFileManager *fileManager=[NSFileManager defaultManager];
	    float folderSize;
	    if ([fileManager fileExistsAtPath:self.cacheDirectoryPath]) {
	        NSArray *childerFiles=[fileManager subpathsAtPath:self.cacheDirectoryPath];
	        for (NSString *fileName in childerFiles) {
	            NSString *absolutePath=[self.cacheDirectoryPath stringByAppendingPathComponent:fileName];
	            folderSize +=[self fileSizeAtPath:absolutePath];
	        }
	        return folderSize;
	    }
	    return 0;
	}
	
如果使用了SDWebImage框架，还要计算SDWebImage缓存大小。

	folderSize += [[SDImageCache sharedImageCache] getSize]/1024.0/1024.0;

###清空缓存

同样的，清空缓存也有两种方式。

清空单个文件。

	#pragma mark -- 清空url接口对应的数据
	- (void)clearDataWithUrl:(NSString *)urlString {
		NSString *pathName = [self getPathWithUrl:urlString];
	    if ([[NSFileManager defaultManager] fileExistsAtPath:pathName]) {
	        NSFileManager *fm = [NSFileManager defaultManager];
	        [fm removeItemAtPath:pathName error:nil];
	    }
	}
	
全部清空。

	#pragma mark -- 清空全部缓存
	- (void)clearAllData {
	    NSFileManager *fm = [NSFileManager defaultManager];
	    [fm removeItemAtPath:self.cacheDirectoryPath error:nil];
	    [self createCacheDirectory];
	}

如果使用了SDWebImage框架，同样的，也需要清除该缓存。

	[[SDImageCache sharedImageCache] cleanDisk];




