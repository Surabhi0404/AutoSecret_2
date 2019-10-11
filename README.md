 
# With Python complete Android APK of the compiler package
 
* Github address: [ https://github.com/Werb/AutoSecret] ( https://github.com/Werb/AutoSecret)
 
### 吐槽
* It real when I finished, and did not find any eggs with 233
* My first thought was that every time I found a fun Android app on Github , I had to download it. Then Gradle build for a long time, and I have to change some parameters. If I can directly install it on my mobile phone, how much better?
* So I thought, if you can write a script and do these things directly, that's fine.
* Although I have never been exposed to Python before , but I know this is a very good language, then try to write it.
* There are many ways to implement, you can use java or direct command- line, but I just want to use python
 
### Executive process
1. From Github Lartet the code ( ZIP packets) down
2. Solutions pressure zip bag
3. Gradle build
4. adb install
 
###使用方法
```
[app]
Sdk_dir = /Users/admin/ Wanbo /code/android/ sdk
Git_download_name = Werb / GankWithZhihu
assembleRelease = False
 
[ dir ]
Base_file_dir = /Users/admin/Desktop
Adb_dir = / Users/admin/ Werb /code/android/ sdk /platform-tools/ adb
Gradle_dir = /users/admin/ werb /code/gradle-2.14.1/bin
Dir_name = AutoSecret
Code_dir_name = SourceCode
Apk_dir_name = Apk
Code_zip_name = GankWithZhihu
```
In auto.config you have to fill
1. sdk path
2. Gradle path
3. adb path
4. To lower load warehouse name
The lower the carrier to the local path
 
Then run the script.
 
Do you feel very troublesome ? I think so too ... but I dig the pit ... I don’t jump, who jumps ...
 
### 有坑坑
* If Gradle and abd are both global and configured into environment variables, there is no need to set the path, just execute it directly.
* If the buildToolsVersion in the project is not available locally , the script is unsuccessful, so you should download the full version.
* So after I finished writing, I found that this is really no egg.
 
 
### the rest of the generation of code
```python
# ! /user/ bin.env python3
# -*- coding: utf-8 -*-
 
# 1. Configuration phase
 
Import os
Import json
Import requests
Import zipfile
 
Try:
  Import configparser
Except ImportError :
  Import ConfigParser
 
Auto_config_path = os.path .dirname (__file__) + '/ auto.config '
 
Try:
  Cf = configparser.ConfigParser ()
  Print('python3')
Except Exception as e:
  Cf = ConfigParser.ConfigParser ()
  Print('python2')
 
# 阅读profile
Cf.read ( Auto_config_path )
 
Sdk_dir = cf.get ( 'app', ' sdk_dir ')
Git_download_name = cf.get ( 'app', ' git_download_name ')
assembleRelease = cf.getboolean ('app', ' assembleRelease ')
Git_download_url = 'https://codeload.github.com/' + git_download_name + '/zip/master'
 
 
# 设置目录
Base_file_dir = cf.get ( ' dir ', ' base_file_dir ')
Dir_name = cf.get ( ' dir ', ' dir_name ')
Code_dir_name = cf.get ( ' dir ', ' code_dir_name ')
Apk_dir_name = cf.get ( ' dir ', ' apk_dir_name ')
Code_zip_name = cf.get ( ' dir ', ' code_zip_name ') + ".zip"
Adb_dir = cf.get ( ' dir ', ' adb_dir ')
Gradle_dir = cf.get ( ' dir ', ' gradle_dir ')
 
file_dir = base_file_dir + '/' + dir_name
Code_dir = file_dir + '/' + code_dir_name
Apk_dir = file_dir + "/" + apk_dir_name
 
 
# 2 under load source
 
# The carrier zip source code
 
If not os.path .exists ( file_dir ):
  Os.mkdir ( file_dir )
Os.chdir ( file_dir )
If not os.path .exists ( code_dir ):
  Os.mkdir ( code_dir )
If not os.path .exists ( apk_dir ):
  Os.mkdir ( apk_dir )
Os.chdir ( code_dir )
 
Def cleanFile ( targetDir ):
  For file in os.listdir ( targetDir ):
      targetFile = os.path .join ( targetDir , file)
      If os.path .isfile ( targetFile ):
          Os.remove ( targetFile )
 
cleanFile ( code_dir )
 
Print ( ' the carrier address :' + git_download_url )
Print ( ' the carrier of ...')
Response = requests.get ( git_download_url )
With open( code_zip_name , " wb ") as code:
  Code.write ( response.content )
Print( response.status _code )
If response.status _code == 200:
  print ( ' the carrier is completed ! ')
Else:
  print ( ' the carrier failed ! ')
 
# De- compression zip package
z = zipfile.ZipFile ( code_zip_name , 'r')
Zip_name = z.namelist ()[0]
Try:
  For file in z.namelist ():
      Z.extract (file, code_dir )
  print ( ' Solution pressed completed ! ')
Except Exception as e:
  print ( ' Solution pressure failure ! ')
 
# Generate local.properties file
Code_dir = code_dir + "/" + zip_name
Def createLocalPropertiesFile ( sourceDir, fileName , root_sdk_dir ):
  If not os.path .exists ( sourceDir ):
      Return
 
  fileDir = sourceDir + '/' + fileName
 
  If os.path .exists ( fileDir ):
      Return
 
  f = open( fileDir , 'w')
  F.write (' sdk.dir =' + root_sdk_dir )
  F.close ()
 
createLocalPropertiesFile ( code_dir , ' local.properties ',  SDK_DIR )
 
# 3.Packing
 
Os.chdir ( code_dir )
# os.system (' chmod 777 gradlew ')
 
Print( ' gradle clean')
the os.system ( gradle_dir + 'Clean')
Print( ' gradle assemble, generate apk ')
 
If assembleRelease :
  Print( ' assembleRelease apk ')
  the os.system ( gradle_dir + ' assembleRelease ')
Else:
  Print( ' assembleDebug apk ')
  the os.system ( gradle_dir + ' assembleDebug ')
 
Print( 'Generate apk finish!')
 
# Copy apk
Def moveApk ( sourceDir ,  targetDir ):
  If not os.path .exists ( sourceDir ):
      Return
 
  For file in os.listdir ( sourceDir ):
       sourceFile = os.path .join ( sourceDir , file)
       targetFile = os.path .join ( targetDir , file)
       If os.path .isfile ( sourceFile ) and ('unaligned' not in os.path.basename ( sourceFile )):
           Open( targetFile , " wb ").write(open( sourceFile , " rb ").read())
 
Source_apk_dir = code_dir + '/' + 'app/build/outputs/ apk '
 
If os.path .exists ( source_apk_dir ):
  moveApk ( source_apk_dir , apk_dir )
 
Print( ' apk copy succeeded ! ')
 
# Install apk
Print( ' abd install')
the os.system ( adb_dir + 'the install' + apk_dir + "/ APP- debug.apk ")
Print(' Installation succeeded ! ')
```
### its real or rewarding
* This time it’s really systematic to know how the Android app is packaged through Gradle .
* Python beginning of body experience, really is a very cool language, simply use the same function than the Java less to write N times the generation of code
* Many wheels made out the reason why , is because for a lazy programmer, why should I repeat these things, simply making a wheel just write it again forget
* Of course, this is not a wheel, I just want to write
* Next, write a multi-channel packaged script in Python , which is still a bit useful.
* If you see here, then go to a star ah
 
## Contact Me
* Email : 1025004680@qq.com
* Weib : [UMR 80] ( http://weibo.com/singerwannber )
* GitHub: [ Werb ] (https://github.com/Werb)
* Blog : [ Werb's Blog] (http://werb.github.io/)
* Stackoverflow : [Werb](http://stackoverflow.com/users/6596737/werb?tab=profile)

