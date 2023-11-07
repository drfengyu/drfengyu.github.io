---
title: 深入浅出Windows应用程序开发(二)
categories:
  - Windows应用程序开发
tags:
  - UWP
sticky: 100
comments: true
toc: true
only:
  - home
  - category
  - tag
abbrlink: 5bdeb856
date: 2022-12-05 10:39:37
pic:
---
#### 应用数据
6.1 应用设置简介
1.拥有容器的层次结构
2.有本地和漫游两种设置类型
Win10支持本地和漫游两种类型,本地是指数据只会存在于当前的客户端应用程序里面,漫游则是指数据会同步到其他设备的相同账户的客户端里.本地应用设置是在根容器ApplicationData.Current.LocalSettings下,而漫游应用设置是在根容器ApplicationData.Current.RoamingSetting下,只是存储的根目录不一样,其他Api操作完全一致.
3.应用设置支持大多数Windows运行时数据类型
应用设置所存储的数据是单个的数据类型对象,它并不是所有的类型都会支持,例如,集合对象不支持,如果要将List<String>对象存储到应用设置里会引起异常信息.
数值类型:UInt8-64 Int16-64 Single Double
布尔类型:Boolean
字符类型:Char16,String
时间类型:DateTime,TimeSpan
结构类型:GUID,Point,Size,Rect
组合类型:ApplicationDataCompositeValue
对于不支持的类型,使用应用文件存储,另一种是将数据序列化为一种受支持的数据类型
6.1.2.应用设置操作
应用设置操作支持增删改查,在开始对应用设置操作之前,首先要获取到应用设置的容器对象(ApplicationDataContainer),所有的操作都会从一个容器对象开始.获取应用程序的根容器可以通过ApplicationData.Current属性来获取单例对象
1.添加和修改应用设置
在进行应用设置相关操作前,需要先获取应用的设置
``` bash
ApplicationDataContainer localSettings=Windows.Storage.ApplicationData.Current.LocalSettings;
```
获取容器之后,将数据添加到应用设置,如果该应用设置已存在,则对其进行修改,使用ApplicationDataContainer.Values属性可以访问在上一步中获取的LocalSettings容器中的设置,然后通过键/值对方式来操作应用设置.
``` bash
localSettings.Values["testSetting"]="Hello Windows10";
```
如果容器里面没有"testSetting"这个键则新增一个,如果已有,则对其进行修改.
使用ApplicationDataContainer.Values属性可以访问在上一步中获取的LocalSetting容器中的设置,然后通过键/值对的方式来操作应用设置.
``` bash
bool isoKeyExist=localSettings.Values.ContainKey("testSetting");
```
2.读取应用设置
从设置中读取数据,也是使用ApplicationDataContainer.Values属性来获取应用设置的值
``` bash
String value=localSettings.Values["testSetting"].ToString();
```
3.删除应用设置
如果需要删除应用程序里面的设置数据,可以用ApplicationDataContainerSettings.Remove方法来实现.
``` bash
localSettings.Values.Remove("testSetting")
```
``` bash
<PivotItem Header="ApplicationDataContainerSettings">
                <StackPanel>
                    <!--输入应用设置的键-->
                    <StackPanel Orientation="Horizontal">
                        <TextBlock x:Name="textBlock2" Text="Key:" Width="150"/>
                        <TextBox x:Name="txtKey" Text="" Width="200"/>
                    </StackPanel>
                    <!--输入应用设置的值-->
                    <StackPanel Orientation="Horizontal" Margin="0 20 0 0">
                        <TextBlock Text="Value:" Width="150"/>
                        <TextBox x:Name="txtValue" Text="" Width="200"/>
                    </StackPanel>
                    <Button Content="保存" x:Name="btnSave" Click="btnSave_Click"/>
                    <Button Content="删除" x:Name="btnDelete"
                            Click="btnDelete_Click"/>
                    <Button Content="清空所有" x:Name="deleteall"
                            Click="deleteall_Click"/>
                    <!--显示容器内所有的键的列表,点击选中可以在上面的输入框里面查看和修改它对应的值-->
                    <TextBlock Text="Keys列表:"/>
                    <ListBox Height="168" x:Name="lstKeys" SelectionChanged="lstKeys_SelectionChanged"/>
                    
                </StackPanel>
              
                
            </PivotItem>

 ///获取当前应用程序的本地设置根容器
            _appSettings = ApplicationData.Current.LocalSettings;
            //把容器的键列表绑定到list控件
            BindKeyList();
 /// <summary>
        /// 将当前程序中所有的key值绑定到List上
        /// </summary>
        private void BindKeyList()
        {
            //先清空List控件的绑定值
            lstKeys.Items.Clear();
            //获取当前应用程序的所有Key
            foreach (var item in _appSettings.Values.Keys)
            {
                //添加到List控件上
                lstKeys.Items.Add(item);
            }
            txtKey.Text = "";
            txtValue.Text = "";
        }

        //声明容器实例
        private ApplicationDataContainer _appSettings;
 /// <summary>
        /// 保存应用设置的值
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void btnSave_Click(object sender, RoutedEventArgs e)
        {
            //检查key输入框不为空
            if (!string.IsNullOrEmpty(txtKey.Text))
            {
                _appSettings.Values[txtKey.Text] = txtValue.Text;
                BindKeyList();
            }
            else
            {
                await new MessageDialog("请输入key值").ShowAsync();
            }
        }
        /// <summary>
        /// 删除在List中选中的应用设置
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void btnDelete_Click(object sender, RoutedEventArgs e)
        {
            //如果选中了List中的某项
            if (lstKeys.SelectedIndex > -1)
            {
                //移除这个键的独立存储设置
                _appSettings.Values.Remove(lstKeys.SelectedItem.ToString());
                BindKeyList();
            }
        }
        /// <summary>
        /// 清空容器所有的设置
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void deleteall_Click(object sender, RoutedEventArgs e)
        {
            _appSettings.Values.Clear();
            BindKeyList();
        }
        //List控件选中项的事件,将选中的键和值显示在上面的文本框中.
        private void lstKeys_SelectionChanged(object sender, SelectionChangedEventArgs e)
        {
            if (e.AddedItems.Count > 0)
            {
                //获取在List中选择的Key
                string key = e.AddedItems[0].ToString();
                //检查设置是否存在这个key
                if (_appSettings.Values.ContainsKey(key)) {
                    txtKey.Text = key;
                    //获取key的值并显示在文本框上
                    txtValue.Text = _appSettings.Values[key].ToString();
                }
            }
        }
```
6.13.存储容器设置
1.容器的创建
容器的创建必须依赖容器的对象,也就是说必须要在容器下面创建容器,所以容器里是可以嵌套着容器的,
但是这个嵌套的层次不能超过32层.首先需要获取根容器,然后调用ApplicationDataContainer.CreateContainer方法,可创建设置容器.该方法有两个参数,第一个是容器的名字,注意同一个容器里面不能有相同名字的两个容器,第二个是ApplicationDataCreateDisposition枚举,通常会设置为枚举中的Always值,表示如果容器不存在则新建一个再返回容器对象.
``` bash
ApplicationDataContainer container=localSettings.CreateContainer("exampleContainer",ApplicationDataCreateDisposition.Always);
```
2.容器的删除
容器的删除可以调用ApplicationDataContainer.DeleteContainer方法,通过传入容器的名称可以删除当前容器下的该名称的容器,注意不是删除容器对象的这个容器.删除容器后,容器下面的应用设置信息也会全部删除掉.所以在做容器删除时一定要确认该容器下面的应用设置信息是否已经完全不需要了,否则会造成信息的丢失.
``` bash
localSettings.DeleteContainer("exampleContainer");
```
``` bash
 <PivotItem Header="ApplicationDataContainer">
                <StackPanel>
                    <Button Content="创建Container" Click="CreateContainer_Click" Margin="2"/>
                    <Button Content="添加信息" Click="WriteSetting_Click" Margin="2"/>
                    <Button Content="删除信息" Click="DeleteSetting_Click" Margin="2"/>
                    <Button Content="删除Container" Click="DeleteContainer_Click" Margin="2"/>
                    <TextBlock x:Name="OutputTextBlock" TextWrapping="Wrap"/>
                </StackPanel>
            </PivotItem>

  ///获取根容器
            localSettings=ApplicationData.Current.LocalSettings;
            //输出容器相关信息
            DisplayOutput();
 /// <summary>
        /// 展示创建的容器和应用设置的信息
        /// </summary>
        private void DisplayOutput()
        {
           //判断容器是否存在
           bool hasContainer=localSettings.Containers.ContainsKey(containerName);
            //判断容器里面的键值是否存在
            bool hasSetting = hasContainer ? localSettings.Containers[containerName].Values.ContainsKey(settingName) : false;
            string output = string.Format(
                $"Container Exists:{(hasContainer?true:false)}\n Setting Exists:{(hasSetting?true:false)}");
            OutputTextBlock.Text = output;
        }

 /// <summary>
        /// 创建一个容器
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void CreateContainer_Click(object sender, RoutedEventArgs e)
        {
            //如果容器不存在则新建
            ApplicationDataContainer container = localSettings.CreateContainer(containerName, ApplicationDataCreateDisposition.Always);
            DisplayOutput();
        }
        //在新建的容器上添加应用设置信息
        private void WriteSetting_Click(object sender, RoutedEventArgs e)
        {
            if (localSettings.Containers.ContainsKey(containerName))
            {
                localSettings.Containers[containerName].Values[settingName] = "Hello World";
            }
            DisplayOutput();

        }
        /// <summary>
        /// 删除容器上的应用设置信息
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void DeleteSetting_Click(object sender, RoutedEventArgs e)
        {
            if (localSettings.Containers.ContainsKey(containerName))
            {
                localSettings.Containers[containerName].Values.Remove(settingName);
            }
            DisplayOutput();
        }
        /// <summary>
        /// 删除所创建的容器
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void DeleteContainer_Click(object sender, RoutedEventArgs e)
        {
            localSettings.DeleteContainer(containerName);
            DisplayOutput();
        }
```
6.14.复合设置数据
应用设置还支持一个特殊的类型 ApplicationDataCompositeValue,就是复合设置.
ApplicationDataCompositeValue类表示必须进行自动序列化和反序列化的相关应用程序的设置.
复合设置通过将其插入设置映射而序列化,通过从映射查找该设置而反序列化.针对少量数据进行了优化,用于大型数据集，性能可能很差.所以复合设置使用的场景通常是将一组互相依赖的数据捆绑在一起,保证在任何情况下,他们都是作为一个整体进行操作.复合设置是应用设置中的一种,所以其相关的操作和应用设置一致.ApplicationDataCompositeValue对象也是一个键/值对的对象类型.
``` bash
ApplicationDataCompositeValue composite=new ApplicationDataCompositeValue();
composite["intVal"]=1;
composite["strVal"]="string";
```
``` bash
 <PivotItem Header="ApplicationDataCompositeValue">
                <StackPanel>
                    <Button Content="创建" Click="WriteCompositeSetting_Click"/>
                    <Button Content="删除" Click="DeleteCompositeSetting_Click"/>
                    <TextBlock x:Name="OutputTextBlock1" TextWrapping="Wrap"/>
                </StackPanel>
            </PivotItem>

 ///ApplicationDataCompositiveValue
            roamingSettings=ApplicationData.Current.LocalSettings;
            //展示信息
            DisplayOutputAdcv();

/// <summary>
        /// 展示复合设置的信息
        /// </summary>
        private void DisplayOutputAdcv()
        {
            ApplicationDataCompositeValue composite = (ApplicationDataCompositeValue)roamingSettings.Values[settingNameA];
            string output;
            if (composite==null)
            {
                output = "复合设置信息为空";
            }
            else
            {
                output =$"复合设置:{settingNameB}={composite[settingNameB]},{settingNameC}={composite[settingNameC]}";
            }
            OutputTextBlock1.Text = output;
        }

 ApplicationDataContainer roamingSettings = null;
        //复合设置的名称
        const string settingNameA = "exampleCompositeSetting";
        //复合设置里面的键One
        const string settingNameB = "one";
        //复合设置里面的键two
        const string settingNameC = "two";
        /// <summary>
        /// 写入复合设置信息
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void WriteCompositeSetting_Click(object sender, RoutedEventArgs e)
        {
            ApplicationDataCompositeValue composite=new ApplicationDataCompositeValue();
            composite[settingNameB] = 1;
            composite[settingNameC] = "world";
            roamingSettings.Values[settingNameA]=composite;
            DisplayOutputAdcv();
        }
        /// <summary>
        /// 删除复合设置信息
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void DeleteCompositeSetting_Click(object sender, RoutedEventArgs e)
        {
            roamingSettings.Values.Remove(settingNameA);
            DisplayOutputAdcv();
        }
```
6.2 应用文件存储
在每个应用的应用数据存储中,该应用拥有系统定义的根目录,一个用于本地文件,一个用于漫游文件,还有一个用于临时文件,应用可向根目录添加新文件和新目录,创建新目录可组织文件,分本地和漫游
6.21.三种类型的应用文件
1.本地应用文件
存储在客户端的存储数据,存储区域属于程序的沙箱里,应用程序自己才可以访问,保障了数据的安全性.应用程序卸载后无法恢复.本地应用文件的根目录文件夹可以通过ApplicationData对象的LocalFolder属性来访问,即ApplicationData.Current.LocalFolder,类型是文件夹(StorageFolder)对象.
2.漫游应用文件
漫游应用文件是指对同一个账号登录的设备共享的数据,用户可以轻松在多个设备保持应用数据同步.漫游应用文件的根目录文件夹可以通过ApplicationData对象的RoamingFolder属性来访问.也就是ApplicationData.Current.RoamingFolder.
 1.数据大小有限制.
大小由ApplicationData类的RoamingStorageQuota属性决定.RoamingStorageQuota属性表示获取可从漫游应用程序数据存储区同步到云的数据的最大值,如果漫游数据在漫游应用程序数据存储区中的当前大小超过RoamingStorageQuota指定的最大值,则系统会挂起并将包中所有应用程序的数据复制到云,直到当前大小不再超过最大值.出于此原因,最好的做法是仅为用户首选项,链接和小型数据文件使用漫游数据.
 2.数据改变时机的不确定性
如果需要监控漫游数据的变化,应用应该注册处理ApplicationData类的DataChanged事件,处理操作在漫游应用程序更改时执行.
 3.数据版本的一致性
更新新版本之前,不会将应用数据更新到用户安装了该应用的其他设备.
 4.漫游数据不是永久的,有时间限制.
漫游数据同步并不是无期限同步的,它有一个30天的时间间隔限制,用户可在此时间间隔访问漫游数据.超过 云删除.卸载 漫游数据保留  在改时间间隔重新安装,会从云中同步漫游数据.
 5.漫游数据同步时机依赖于网络和设备
随机 漫游数据是应用设置,可以通过特殊的设置键来设置一个高优先级别的漫游设置数据,可以更快频繁和快速同步到云端.这个高优先级别的key为HighPriority,系统会以最快的速度在多个设备间同步HighPriority所对应的数据.它支持ApplicationDataCompositeValue数据,但总大小限于8KB,限值不是强制的,超过视为常规漫游。
3.临时应用文件
类似缓存,不会漫游,随时删除.磁盘清理清除 用于存储应用会话期间的临时信息,无法保证超出应用会话结束时间后仍将保留此数据.临时应用文件的根目录文件夹可以通过ApplicationData对象的TemporaryFolder属性来访问,也就是ApplicationData.Current.TemporaryFolder.
6.22.应用文件和文件夹操作
win10对应用数据所进行的操作,就是对应用文件夹和文件的操作.
StorageFolder类表示操作文件夹及其内容,并提供有关它们的信息,用于向本地文件夹内的某个文件读取和写入数据.在设备存储里文件夹的根目录分别为本地文件夹(ApplicationData.Current.LocalFolder),漫游文件夹(ApplicationData.Current.RoamingFolder)和临时文件夹(ApplicationData.Current.TemporaryFolder).
Storage类表示文件,提供有关文件及其内容和操作信息.StorageFolder类和StorageFile类是两个关系非常密切的类
StorageFolder类的主要成员
DateCreated 获取创建文件夹的日期和时间
Name 获取存储文件夹的名称
Path 获取存储文件夹的路径
CreteFileAsync(string desiredName) 在文件夹或文件组中创建一个新文件,desiredName:要创建的文件的所需名称,返回表示新文件的StorageFile
CreateFolderAsync(string desiredName) 在当前文件夹中创建新的文件夹.desiredName:要创建的文件夹所需名称.返回表示新文件夹的StorageFolder.
DeleteAsync(); 删除当前文件夹或文件组.
GetFileAsync(string name) 从当前文件夹获取指定文件.name:要检索的文件的名称.返回表示文件的StorageFile.
GetFilesAsync() 在当前文件夹中获取文件.返回文件夹中的文件列表(类型IReadOnlyList<StorageFile>).列表中的每个文件均由一个StorageFile对象表示.
GetFolderAsync(string name) 从当前文件夹获取指定文件夹.name:要检索的文件夹的名称.返回表示子文件夹的StorageFolder.
RenameAsync(string desiredName) 重命名当前文件夹.desiredName:当前文件夹所需的新名称.
StorageFile类的主要成员
DateCreated 获取创建文件夹的日期和时间
Name 获取存储文件夹的名称
Path 获取存储文件夹的路径
CopyAndReplaceAsync(IStorageFile fileToReplace) 将指定文件替换为当前文件的副本.fileToReplace:要替换的文件
CopyAsync(IStorageFolder destinationFolder):在指定文件夹中创建文件的副本.destinationFolder:从中创建副本的目标文件夹.返回表示副本的StorageFile
CopyAsync(IStorageFolder destinationFolder,string desiredNewName) 使用所需的名称,在指定文件夹中创建文件的副本.destinationFolder:从中创建副本的目标文件夹.desiredNewName:副本的所需名称.如果在已经指定desiredNewName的目标文件夹中存在现有文件,则为副本生成唯一的名称.返回表示副本的StorageFile
DeleteAsync() 删除当前文件
GetFileFromPathAsync(string path) 获取StorageFIle对象以代表指定路径中的文件.path:表示获取StorageFile的文件路径.返回表示文件的StorageFile
RenameAsync(string desiredName) 重命名当前文件.desiredName:当前项所需的新名称.
1.创建文件夹和文件
对于根目录里的StorageFolder对象,可以直接通过ApplicationData类的单例来获取,在文件夹里面再创建文件夹,可以调用StorageFolder.CreateFolderAsync方法在本地文件夹中创建一个文件夹目录,以及调用StorageFolder.CreateFileAsync方法在本地文件夹中创建一个文件.
``` bash
/// <summary>
        /// 创建文件夹和文件
        /// </summary>
        private async void GetLocalFolder()
        {
            //获取本地文件夹根目录
            StorageFolder local = Windows.Storage.ApplicationData.Current.LocalFolder;
            //创建文件夹,如果文件夹存在则打开它
            var dataFolder =await local.CreateFolderAsync("DataFolder",CreationCollisionOption.OpenIfExists);
            //创建一个命名为DataFile.txt的文件,如果文件存在则替换掉
            var file = await dataFolder.CreateFileAsync("DataFile.txt",CreationCollisionOption.ReplaceExisting);

        }
```
2.文件的读写
可以使用StreamReader,StreamWriter类,FileIO类
使用S可以读取/写入标准文本文件的各行信息.StreamReader/StreamWriter的默认编码为UTF-8.UTF-8可以正确处理Unicode字符并提供一致的结果.FileIO类是专门为IStorageFile类型的对象表示读取/写入文件提供帮助的方法,FileIO类是一个静态类,直接调用其静态的文件读写方法来进行操作.
``` bash
/// <summary>
        /// 2.文件的读写
        /// </summary>
        private async void ReadWrite(StorageFile file) {
            //读取文件的文本信息,使用FileIO类实现
            string fileContent = await FileIO.ReadTextAsync(file);
            //读取文件的文本信息,使用StreamReader类实现
            using (StreamReader streamReader = new StreamReader(file.Path))
            {
                fileContent = streamReader.ReadToEnd();

            }
            //写入文件的文本信息,使用FileIO类实现
            await FileIO.WriteTextAsync(file, "Windows 10");
            //写入文件的文本信息,使用StreamWriter类实现.
            using (StreamWriter swNew=new StreamWriter(file.Path))
            {
                swNew.WriteLine("Windows 10");
            }

        }
```
还可以使用WindowsRuntimeStorageExtensions类提供的实现IStorageFile和IStorageFolder接口类使用的方法来进行文件和文件夹的读写操作,这些扩展的方法分别有OpenStreamForReadAsync和OpenStreamForWriteAsync.
``` bash
  //使用OpenStreamForWriteAsync方法来实现写入文件信息
            byte[] fileBytes=Encoding.UTF8.GetBytes("Windows 10".ToCharArray());
            using (var s=await file.OpenStreamForWriteAsync())
            {
                s.Write(fileBytes,0,fileBytes.Length);
            }
           ///文件删除,复制,重命名和移动操作
            //await file.DeleteAsync();
            var newfile =await storageFolder.CreateFileAsync("sample - Copy.txt", CreationCollisionOption.ReplaceExisting);
            //复制文件
            StorageFile fileCopy = await file.CopyAsync(storageFolder,"sample - Copy.txt",NameCollisionOption.ReplaceExisting);
            //重命名文件
            await file.RenameAsync("sampleRe.txt");
            var newStorageFolder = await storageFolder.CreateFolderAsync("newDataFolder", CreationCollisionOption.OpenIfExists);
            //移动文件
            await file.MoveAsync(newStorageFolder,"newDataFile");
 //文件名
        private string fileName = "testfile.txt";
        //保存按钮事件处理程序
        private async void bt_save_Click(object sender, RoutedEventArgs e)
        {
            if (infoStorage.Text!="")
            {
                //写入文件信息
                await WriteFile(fileName,infoStorage.Text);
                await new MessageDialog("保存成功").ShowAsync();
            }
            else
            {
                await new MessageDialog("内容不能为空").ShowAsync();
            }
        }
        /// <summary>
        /// 写入本地文件夹根目录的文件
        /// </summary>
        /// <param name="fileName"></param>
        /// <param name="text"></param>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        private async Task WriteFile(string fileName, string text)
        {
            //获取本地文件夹根目录文件夹
            IStorageFolder applicationFolder=ApplicationData.Current.LocalFolder;
            //在文件夹里面创建文件,如果文件存在则替换掉.
            IStorageFile storageFile = await applicationFolder.CreateFileAsync(fileName,CreationCollisionOption.OpenIfExists);
            //使用FileIO类把字符串信息写入文件
            await FileIO.WriteTextAsync(storageFile, text);
        }
        /// <summary>
        /// 删除文件
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void bt_delete_Click(object sender, RoutedEventArgs e)
        {
            string text;
            try
            {
                IStorageFolder applicationFolder = ApplicationData.Current.LocalFolder;
                //获取文件
                IStorageFile storageFile=await applicationFolder.GetFileAsync(fileName);
                //删除当前的文件
                await storageFile.DeleteAsync();
                text = "删除成功";
            }
            catch (Exception ex)
            {

                text = "文件删除错误:" + ex.Message;
            }
            await new MessageDialog(text).ShowAsync();
        }
        /// <summary>
        /// 读取文件
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void bt_read_Click(object sender, RoutedEventArgs e)
        {
            //读取文件的文本信息
            string content = await ReadFile(fileName);
            await new MessageDialog(content).ShowAsync();
        }
        /// <summary>
        /// 读取本地文件夹根目录的文件
        /// </summary>
        /// <param name="fileName"></param>
        /// <returns></returns>
        /// <exception cref="NotImplementedException"></exception>
        private async Task<string> ReadFile(string fileName)
        {
            string text;
            try
            {
                //获取本地文件夹根目录文件夹
                IStorageFolder applicaitonFolder=ApplicationData.Current.LocalFolder;
                //根据文件名获取文件夹里面的文件
                IStorageFile storageFile=await applicaitonFolder.GetFileAsync(fileName);
                //打开文件获取文件的数据流
                IRandomAccessStream accessStream=await storageFile.OpenReadAsync();
                //使用StreamReader读取文件的内容,需要将IRandomAccessStream对象转换为Stream对象来初始化StreamReader对象.
                using (StreamReader streamReader=new StreamReader(accessStream.AsStreamForRead((int)accessStream.Size)))
                {
                    text = streamReader.ReadToEnd();
                }
            }
            catch (Exception e)
            {
                text = "文件读取错误:" + e.Message;
              
            }
            return text;
        }

```
6.23.文件Stream和Buffer读写操作
操作二进制文件,需要用到DataWriter类和DataReader类,DataWriter类用于写入文件的信息.当然这个信息不仅仅是文本信息
1.Buffer的写入操作
win10里,文件的Buffer操作使用的是IBuffer对象,所以要使用DataWriter类写入相关的信息之后再转换为IBuffer对象,然后保存到文件中.
``` bash
using(InMemoryRandomAccessStream memoryStream=new InMemoryRandomAccessStream()){
	using(DataWriter dataWriter=new DataWriter(memoryStream)){
	//文件相关的信息,可以根据文件的规则来进行写入
	dataWriter.WriterInt32(size);
	dataWriter.WriteString(userContent);
	buffer=dataWriter.DetachBuffer();
}
}
await FileIO.WriteBufferAsync(file,buffer);
```
2.Buffer的读取操作
读取的操作其实就是获取文件的IBuffer对象之后,再使用IBuffer对象初始化一个DataReader对象,就可以对文件进行读取操作了.
``` bash
IBuffer buffer=await FileIO.ReadBufferAsync(file);
using(DataReader dataReader=DataReader.FromBuffer(buffer)){
	//读取文件相关的信息,读取的规则要与文件的规则一致.
	Int32 stringSize=dataReader.ReadInt32();
	string fileContent=dataReader.ReadString((uint)stringSize);
}
```
3.Stream的写入操作
文件的Stream其实就是文件内的信息,所以在用Stream来写入文件的数据时,直接保存Stream的信息就可以,并不需要再调用文件的对象进行保存.
``` bash
using(StorageStreamTransaction transaction=await file.OpenTransactedWriteAsync()){
	using(DataWriter dataWriter=new DataWriter(transaction.Stream)){
	//文件相关的信息,可以根据文件的规则进行写入
	dataWriter.WriteInt32(size);
	dataWriter.WriteString(userContent);
	... ...
	transaction.Stream.Size=await dataWriter.StoreAsync();
	//保存Stream数据
	await transaction.CommitAsync();
}
}
```
4.Stream的读取操作
使用Stream读取文件的内容,需要先调用DataReader类的LoadAsync方法,把数据加载进来,再调用相关的Read方法来读取文件的内容;Buffer的操作不用调用LoadAsync方法,那是因为其已经一次性把数据都读取出来了.
``` bash
using(IRandomAccessStream readStream=await file.OpenAsync(FileAccessMode.Read)){
	//读取文件相关的信息,读取的规则要与文件的规则一致
	await dataReader.LoadAsync(sizeof(Int32));
	Int32 stringSize=dataReader.ReadInt32();
	await dataReader.LoadAsync((UInt32)stringSize);
	string fileContent=dataReader.ReadString((uint)stringSize);
	... ...	
}
```
文件Stream和Buffer读写操作的示例,演示了文件格式是先写入字符串内容的长度,Int32类型占用4个字节,然后再写入字符串的内容;读取文件的时候再按照这样的方式逆着来.按文件格式读取正确数据.
``` bash
<PivotItem Header="StreamBuffer">
                <StackPanel>
                    <Button x:Name="bt_crreate" Content="创建一个测试文件" Margin="2" Click="bt_crreate_Click"/>
                    <Button x:Name="bt_writebuffer" Content="写入buffer" Margin="2" Click="bt_writebuffer_Click"/>
                    <Button x:Name="bt_readbuffer" Content="读取buffer" Margin="2" Click="bt_readbuffer_Click"/>
                    <Button x:Name="bt_writestream" Content="写入stream" Margin="2" Click="bt_writestream_Click"/>
                    <Button x:Name="bt_readstream" Content="读取stream" Margin="2" Click="bt_readstream_Click"/>
                    <TextBlock x:Name="StreambufferTextBlock" TextWrapping="Wrap" FontSize="20"/>
                </StackPanel>
            </PivotItem>

   /// <summary>
        /// 创建的测试文件对象
        /// </summary>
        private StorageFile sampleFile;
        /// <summary>
        /// 文件名
        /// </summary>
        private string filename = "sampleFile.dat";
        /// <summary>
        /// 创建一个文件事件处理程序
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void bt_crreate_Click(object sender, RoutedEventArgs e)
        {
            StorageFolder storageFolder= ApplicationData.Current.LocalFolder;
            sampleFile=await storageFolder.CreateFileAsync(filename,CreationCollisionOption.ReplaceExisting);
            StreambufferTextBlock.Text = $"文件{sampleFile.Name}已经创建好.";

        }
        /// <summary>
        /// 写入IBuffer按钮事件处理程序
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void bt_writebuffer_Click(object sender, RoutedEventArgs e)
        {
            StorageFile file = sampleFile;
            if (file!=null)
            {
                try
                {
                    string userContent = "测试的文本消息";
                    IBuffer buffer;
                    //使用一个内存的可访问的数据流创建一个DataWriter对象,写入String 再转换为IBuffer对象.
                    using (InMemoryRandomAccessStream memoryStream=new InMemoryRandomAccessStream())
                    {
                        //把String信息转换为IBuffer对象.
                        using (DataWriter dataWriter=new DataWriter(memoryStream))
                        {
                            //先写入字符串的长度信息
                            dataWriter.WriteInt32(Encoding.UTF8.GetByteCount(userContent));
                            //写入字符串信息
                            dataWriter.WriteString(userContent);
                            buffer=dataWriter.DetachBuffer();
                        }
                    }
                    await FileIO.WriteBufferAsync(file,buffer);
                    StreambufferTextBlock.Text = $"长度为:{buffer.Length} bytes的文本信息写入到了文件{file.Name}:{Environment.NewLine+Environment.NewLine+userContent}";
                }
                catch (Exception ex)
                {
                    StreambufferTextBlock.Text =$"异常:{ex.Message}";
                }
            }
            else
            {
                StreambufferTextBlock.Text = "请先创建文件";
            }
        }


        /// <summary>
        /// 读取IBuffer按钮事件处理程序
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void bt_readbuffer_Click(object sender, RoutedEventArgs e)
        {
            StorageFile file= sampleFile;
            if (file != null)
            {
                try
                {
                    IBuffer buffer = await FileIO.ReadBufferAsync(file);
                    using (DataReader dataReader = DataReader.FromBuffer(buffer))
                    {
                        //先读取字符串的长度信息
                        Int32 stringSize = dataReader.ReadInt32();         //读取字符串信息
                        string fileContent = dataReader.ReadString((uint)stringSize);
                        StreambufferTextBlock.Text = $"长度为:{buffer.Length} bytes的文本信息从文件{file.Name}读取出来,其中字符串的长度为{stringSize}bytes:{Environment.NewLine + fileContent}";
                    }
                }
                catch (Exception ex)
                {

                    StreambufferTextBlock.Text = "异常:" + ex.Message;
                }
            }
            else {
                StreambufferTextBlock.Text = "请先创建文件";
            }
        }
        //写入Stream按钮事件处理程序.
        private async void bt_writestream_Click(object sender, RoutedEventArgs e)
        {
            StorageFile file= sampleFile;
            if (file!=null)
            {
                try
                {
                    string userContent = "测试的文本信息";
                    //使用StorageStreamTransaction对象来创建DataWriter对象写入数据
                    using (StorageStreamTransaction transaction=await file.OpenTransactedWriteAsync())
                    {
                        using (DataWriter dataWriter=new DataWriter(transaction.Stream))
                        {
                            //先写入信息的长度
                            dataWriter.WriteInt32(Encoding.UTF8.GetByteCount(userContent));
                            //写入字符串信息
                            dataWriter.WriteString(userContent);
                            //提交dataWriter的数据同时重设Stream的大小
                            transaction.Stream.Size=await dataWriter.StoreAsync();
                            //保存Stream数据
                            await transaction.CommitAsync();
                            StreambufferTextBlock.Text = $"使用stream把信息写入了文件{file.Name}:{Environment.NewLine+userContent}";
                        }
                    }
                }
                catch (Exception ex)
                {

                    StreambufferTextBlock.Text = $"异常:" + ex.Message;
                }
            }
            else
            {
                StreambufferTextBlock.Text = "请先创建文件";
            }
        }
        /// <summary>
        /// 读取Stream按钮事件处理程序
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void bt_readstream_Click(object sender, RoutedEventArgs e)
        {
            StorageFile file=sampleFile;
            if (file!=null)
            {
                try
                {
                    //使用文件流创建DataReader对象
                    using (IRandomAccessStream readStream=await file.OpenAsync(FileAccessMode.Read))
                    {
                        using (DataReader dataReader=new DataReader(readStream))
                        {
                            UInt64 size = readStream.Size;
                            if (size<=UInt32.MaxValue)
                            {
                                //先读取字符串的长度信息
                                await dataReader.LoadAsync(sizeof(Int32));
                                Int32 stringSize=dataReader.ReadInt32();
                                //读取字符串的内容信息
                                await dataReader.LoadAsync((UInt32)stringSize);
                                string fileContent = dataReader.ReadString((UInt32)stringSize);
                                StreambufferTextBlock.Text =$"使用stream把信息从文件{file.Name}读取出来,其中字符串的长度为:{stringSize}bytes:{Environment.NewLine+fileContent}";
                            }
                            else
                            {
                                StreambufferTextBlock.Text = $"文件{file.Name}太大,不能再单个数据块中读取";
                            }
                        }
                    }
                }
                catch (Exception ex)
                {

                    StreambufferTextBlock.Text = $"异常:{ex.Message}";
                }

            }
            else
            {
                StreambufferTextBlock.Text = "请先创建文件";
            }
        }
```
6.24.应用文件的URI方案
3个根目录文件夹所对应的保存路径的格式分别如下:
1.LocalFolder文件夹保存路径格式:%USERPROFILE%\APPDATA\Local\Packages\{PackageId}\LocalState.
2.RoamingFolder文件夹的保存路径格式:%USERPROFILE%\APPDATA\Local\Packages\{PackageId}\RoamingState
3.TemporaryFolder文件夹的保存路径格式:%USERPROFILE%\APPDATA\Local\Packages\{PackageId}\TempState
获取到的路径并不能作为访问文件的路径来使用,访问文件的路径需要使用本地文件夹的ms-appdata的URI方案.LocalFolder文件夹对应的是“ms-appdata:///local/”,RoamingFolder "ms-appdata:///roaming/"
TemporaryFolder ms-appdata:///temp/ 可以根据StorageFile类的静态方法GetFileFromApplicationUriAsync来根据URI读取文件.下面示例使用了ms-appdata的URI方法来获取在LocalFolder文件夹里面的AppConfigSettings.xml文件.

``` bash
var file=await StorageFile.GetFileFromApplicationUriAsync(new Uri("ms-appdata:///local/AppConfigSettings.xml"));
```
在这种通过URI访问文件的方案里还需要注意,新文件和文件夹的路径在URI方案名称的最后一个斜杠后面不能超过185个字符.
``` bash
 <PivotItem Header="GetFolderByUri">
                <StackPanel>
                    <TextBox Header="文件信息:" x:Name="UriInfo" TextWrapping="Wrap"/>
                    <Button x:Name="bt_saveuri" Content="创建文件" Click="bt_save_Click_1"/>
                    <Button x:Name="bt_readuri" Content="通过URI读取文件" Margin="2" Click="bt_read_Click_1"/>
                    
                </StackPanel>
            </PivotItem>
 //文件名
        private string UrifileName = "testfile.txt";
        
        

        /// <summary>
        /// 读取文件
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void bt_read_Click_1(object sender, RoutedEventArgs e)
        {
            //读取文件的文本信息
            string text;
            try
            {
                //通过URI获取本地文件
                var storageFile = await StorageFile.GetFileFromApplicationUriAsync(new Uri("ms-appdata:///local/"+UrifileName));
                //打开文件获取文件的数据流
                IRandomAccessStream accessStream = await storageFile.OpenReadAsync();
                //使用StreamReader读取文件的内容,需要将IRandomAccessStream对象转换为Stream对象来初始化StreamReader对象.
                using (StreamReader streamReader=new StreamReader(accessStream.AsStreamForRead((int)accessStream.Size)))
                {
                    text = streamReader.ReadToEnd();
                }
            }
            catch (Exception ex)
            {
                text = $"文件读取错误:"+ex.Message;
                
            }
            await new MessageDialog(text).ShowAsync();
        }
        //创建文件
        private async void bt_save_Click_1(object sender, RoutedEventArgs e)
        {
            if (UriInfo.Text != "") { 
                //获取本地文件夹根目录文件夹
                IStorageFolder applicationFolder=ApplicationData.Current.LocalFolder;
                //在文件夹里面创建文件,如果文件存在则替换掉
                IStorageFile storageFile = await applicationFolder.CreateFileAsync(UrifileName,CreationCollisionOption.OpenIfExists);
                //使用FileIO类把字符串信息写入文件
                await FileIO.WriteTextAsync(storageFile, UriInfo.Text);
                await new MessageDialog($"保存成功,文件的路径:{storageFile.Path}").ShowAsync();
            }
            else
            {
                await new MessageDialog("内容不能为空").ShowAsync();

            }
        }

```
6.3.常用的存储数据格式
6.31.JSON数据序列化存储
JSON用于描述数据结构,有以下形式存在.
对象(object):一个对象以"{"开始","并以"}"退出.一个对象包含一系列非排序的名称/值对,每个名称/值对之间使用","分隔.
名称/值(collection):名称和值之间使用":"隔开,一般的形式是{name;value}.一个名称是一个字符串,一个值可以是一个字符串,一个数值,一个对象,一个布尔值,一个有串行表或者一个null值.值的串行表(Array):一个或者多个值用","分区后,使用"[","]"括起来就形成了这样的列表.例如,[collection,collection]
在win10里如果要使用JSON数据格式来存储相关的信息会有两种编程方式;
1.使用DataContractJsonSerializer类对JSON数据进行序列化和反序列化
DataContractJsonSerializer类对Json数据进行序列化和反序列化,是最简洁的Json数据操作方式.序列化的过程是把实体类对象转换为Json字符串对象
``` bash
 /// <summary>
        /// 对实体类进行序列化的方法
        /// </summary>
        /// <param name="item"></param>
        /// <returns></returns>
        public string ToJsonData(object item) { 
            DataContractJsonSerializer serializer=new DataContractJsonSerializer(item.GetType());
            string result = string.Empty;
            using (MemoryStream ms=new MemoryStream())
            {
                serializer.WriteObject(ms,item);
                ms.Position = 0;
                using (StreamReader reader=new StreamReader(ms))
                {
                    result = reader.ReadToEnd();
                }
            }
            return result;
        }
        /// <summary>
        /// 把Json字符串反序列化成实体类对象
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="jsonString"></param>
        /// <returns></returns>
        public T DataContractJsonDeSerializer<T>(string jsonString) { 
        var ds=new DataContractJsonSerializer(typeof(T));
            var ms=new MemoryStream(Encoding.UTF8.GetBytes(jsonString));
            T obj=(T)ds.ReadObject(ms);
            ms.Dispose();
            return obj;
        }
```
2.使用JsonObject对象来自定义Json对象
使用DataContractJsonSerializer类对json数据进行序列化和反序列化的操作很方便,但是却有一个弊端,它的灵活性很差.例如,序列化成的Json字符串的名称/值对的名称必须和类的属性完全一致.若要实现灵活复杂的Json数据进行序列化和反序列化的操作可以使用JsonObject类来进行自定义.
通过JsonObject类来创建一个如下的Json对象.
{"city":"Beijing","street":"Chaoyang Road","postcode":100025}
创建其对应的JsonObject类实现的语法如下:
``` bash
JsonObject jsonObject=new JsonObject();
JsonObject.SetNamedValue("city",JsonValue.CreateStringValue("Beijing"));
JsonObject.SetNamedValue("street",JsonValue.CreateStringValue("Chaoyang Road"));
jsonObject.setNamedValue("postcode",JsonValue,CreateNumberValue(10025));
```
获取JsonObject类对象"city"所对应的数值如下:
jsonObject.GetNamedString("city","默认值");
``` bash
<PivotItem Header="JsonObject">
                <StackPanel>
                    <TextBlock Text="Write your message" FontSize="20"/>
                    <TextBox x:Name="userName" Header="Name:"/>
                    <TextBox x:Name="userAge" Header="Age:" InputScope="Number"/>
                    <TextBlock Text="Your study school:" FontSize="20"/>
                    <CheckBox Content="Haerbing School" x:Name="school1"/>
                    <CheckBox Content="LanXiang School" x:Name="school2"/>
                    <StackPanel Orientation="Horizontal" HorizontalAlignment="Center">
                        <Button Content="保存" x:Name="save" Click="save_Click"/>
                        <Button Content="获取保存的信息" x:Name="get" Click="get_Click"/>
                        
                    </StackPanel>
                    <TextBlock x:Name="JsonObjectInfo" TextWrapping="Wrap" FontSize="20"/>
                </StackPanel>
            </PivotItem>
 /// <summary>
        /// 对实体类进行序列化的方法
        /// </summary>
        /// <param name="item"></param>
        /// <returns></returns>
        public string ToJsonData(object item) { 
            DataContractJsonSerializer serializer=new DataContractJsonSerializer(item.GetType());
            string result = string.Empty;
            using (MemoryStream ms=new MemoryStream())
            {
                serializer.WriteObject(ms,item);
                ms.Position = 0;
                using (StreamReader reader=new StreamReader(ms))
                {
                    result = reader.ReadToEnd();
                }
            }
            return result;
        }
        /// <summary>
        /// 把Json字符串反序列化成实体类对象
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="jsonString"></param>
        /// <returns></returns>
        public T DataContractJsonDeSerializer<T>(string jsonString) { 
        var ds=new DataContractJsonSerializer(typeof(T));
            var ms=new MemoryStream(Encoding.UTF8.GetBytes(jsonString));
            T obj=(T)ds.ReadObject(ms);
            ms.Dispose();
            return obj;
        }

        private const string UserDataKey = "UserDataKey";
        private async void save_Click(object sender, RoutedEventArgs e)
        {
            if (userName.Text=="" || userAge.Text=="")
            {
                await new MessageDialog("请输入完整的信息").ShowAsync();
                return;
            }
            ObservableCollection<School> education = new ObservableCollection<School>();
            if (school1.IsChecked == true) {
                education.Add(new School { Id="id001",Name=school1.Content.ToString()});
               

            }
            if (school2.IsChecked == true)
            {
                education.Add(new School { Id = "id002", Name = school2.Content.ToString() });
            }

            User user = new User { Education=education,Id=Guid.NewGuid().ToString(),Name=userName.Text,Age=Int32.Parse(userAge.Text),Verified=false};
            //使用DataContractJsonDeSerializer的实现方式
            //string json=ToJsonData(user);
            //使用JsonObject的实现方式
            string json = user.Stringify();
            JsonObjectInfo.Text = json;
            _appSettings.Values[UserDataKey] = json;
            await new MessageDialog("保存成功").ShowAsync();
        }
        // 把应用设置存储的Json数据转换为实体类对象.
        private async void get_Click(object sender, RoutedEventArgs e)
        {
            if (!_appSettings.Values.ContainsKey(UserDataKey)) {
                await new MessageDialog("未保存信息").ShowAsync();
                return;
            }
            string json = _appSettings.Values[UserDataKey].ToString();
            //使用DataContractJsonDeSerializer的实现方式
            //User user = DataContractJsonDeSerializer<User>(json);
            //使用JsonObject的实现方式
            User user = new User(json);
            string userInfo = "";
            userInfo = $"Id:{user.Id} Name:{user.Name} Age:{user.Age}";
            foreach (var item in user.Education)
            {
                userInfo += $"Education:Id:{item.Id} Name:{item.Name}";
            }
            await new MessageDialog(userInfo).ShowAsync();
        }

```
6.32.XML文件存储
Xml是可扩展标记语言的缩写,用于创建内容并使用限定标记,从而使每个单词,短语或块称为可识别，可分类的信息.Xml是一种易于使用和扩展的标记语言,它比JSON使用得更加广泛,它也是一种简单的数据格式,是纯100%的ASCII文本,而ASCII的抗破坏能力是很强的 下面是一个非常简单的XML文档的格式
``` bash
<?xml version="1.0" enconding="UTF-8">
<test>
Hello Windows 10
</test>

```
简写:<? xml?>
1.使用DataContractSerializer类对Xml文件进行序列化和反序列化
DataContractSerializer类和DataContractJsonSerializer类是两个很相似的类,前者是针对Xml格式的数据,后者是针对Json的数据.
Person对象Name="terry",Age=41,通常上面的序列化方法存储到应用文件里.
``` bash
<Person xmlns:i="http://www.w3.org/2001/XMLSchema - instance" xmlns="http://schemas.datacontract.org/2004/07/DataContractSerializerDeno"><Age>41</Age>
<Name>terry</Name>
</Person>
```
2.使用XmlDocument类对Xml文件进行序列化和反序列化
``` bash
 /// <summary>
        /// 把实体类对象序列化成xml格式存储到文件里面
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="data"></param>
        /// <param name="fileName"></param>
        /// <returns></returns>
        public async Task SaveAsync<T>(T data, string fileName) {
            StorageFile file = await ApplicationData.Current.LocalFolder.CreateFileAsync(fileName,CreationCollisionOption.ReplaceExisting);
            //获取文件的数据流来进行操作.
            using (IRandomAccessStream raStream=await file.OpenAsync(FileAccessMode.ReadWrite))
            {
                using (IOutputStream outputStream=raStream.GetOutputStreamAt(0))
                {
                    //创建序列化对象写入数据
                    DataContractSerializer serializer = new DataContractSerializer(typeof(T));
                    serializer.WriteObject(outputStream.AsStreamForWrite(),data);
                    await outputStream.FlushAsync();
                }
            }
        }
        /// <summary>
        /// 反序列化XML文件
        /// </summary>
        /// <typeparam name="T"></typeparam>
        /// <param name="filename"></param>
        /// <returns></returns>
        public async Task<T> ReadAsync<T>(string filename) {
            //获取实体类类型实例化一个对象
            T sessionState_ = default(T);
            StorageFile file = await ApplicationData.Current.LocalFolder.GetFileAsync(filename);
            if (file == null) return sessionState_;
            using (IInputStream inStream=await file.OpenSequentialReadAsync())
            {
                //反序列化XML数据
                DataContractSerializer serializer = new DataContractSerializer(typeof(T));
                sessionState_ = (T)serializer.ReadObject(inStream.AsStreamForRead());
            }
            return sessionState_;
        }
        /// <summary>
        /// 
        /// </summary>
        public void SaveAsXml() {
            //创建一个XmlDocument对象代表着是一个Xml的文档对象.
            XmlDocument dom = new XmlDocument();
            //创建和添加一条XML的评论
            XmlComment dec = dom.CreateComment("THis is Students Message.");
            dom.AppendChild(dec);

            //添加一个students的元素为Xml文档的根元素
            XmlElement x = dom.CreateElement("studetns");
            dom.AppendChild(x);
            //创建一个Student元素后面再添加到students元素的节点上
            XmlElement x1 = dom.CreateElement("Student");
            //在Student元素里再添加id和name两个元素节点,在节点内还添加了文本内容
            XmlElement x11 = dom.CreateElement("id");
            x11.InnerText = "id001";
            x1.AppendChild(x11);
            XmlElement x12 = dom.CreateElement("name");
            x12.InnerText = "张三";
            x1.AppendChild(x12);
            x.AppendChild(x1);

        }
```
XML文档操作相关的类和说明
Document  XmlDocument类  树中所有节点的容器,也称作文档根,文档根并非总是与根元素相同
DocumentFragment  XmlDocumentFragment类  包含一个或多个不带任何树结构的节点的临时段
DocumentType XmlDocumentType类    <! DOCTYPE...>节点
EntityReference XmlEntityReference类  非扩展的实体引用文本
Element XmlElement类 元素节点
Attr XmlAttribute类  元素的属性
ProcessingInstruction XmlProcessingInstruction类 处理指令节点
Comment XmlComment类 注释节点
Text XmlText类 属于某个元素或属性的文本
CDATASection XmlCDataSection类 CDATA
Entity XmlEntity类 XML文档(来自内部文档类型定义(DTD)子集或来自外部DTD和参数实体)中的<!ENTITY...>声明
Notation XmlNotation类 DTD中声明的表示法
``` bash
 <PivotItem Header="Xml">
                <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
                    <Grid x:Name="ContentPanelShop" Margin="12,0,12,0">
                        <!--展示购物清单的列表-->
                        <ListBox FontSize="48" x:Name="Files">
                        </ListBox>
                    </Grid>
                </Grid>
            </PivotItem>
        </Pivot>
 <AppBarButton Label="新增" Icon="Add" Click="AppBarButton_Click_1"/>
 //加载页面触发Loaded事件
            Loaded += BlankPage_Loaded;
  private async void BlankPage_Loaded(object sender, RoutedEventArgs e)
        {
            Files.Items.Clear();
            //获取应用程序的本地存储文件
            StorageFolder storage = await ApplicationData.Current.LocalFolder.CreateFolderAsync("ShoppingList",CreationCollisionOption.OpenIfExists);
            var files = await storage.GetFilesAsync();
            {
                //获取购物清单文件夹里面存储的文件
                foreach (StorageFile file in files) {
                    //动态构建一个Grid
                    Grid a = new Grid();
                    //定义第一列
                    ColumnDefinition col = new ColumnDefinition();
                    col.Width = GridLength.Auto;
                    a.ColumnDefinitions.Add(col);

                    //定义第二列
                    ColumnDefinition col2 = new ColumnDefinition();
                    col2.Width = GridLength.Auto;
                    a.ColumnDefinitions.Add(col2);
                    //添加一个TextBlock现实文件名到第一列
                    TextBlock txbx = new TextBlock();
                    txbx.Text = file.DisplayName;
                    Grid.SetColumn(txbx,0);
                    //添加一个HyperlinkButton链接到购物详细清单页面,这是第二列
                    HyperlinkButton btn = new HyperlinkButton();
                    btn.Content = "查看详细";
                    btn.Name = file.DisplayName;
                    btn.Click += (s,ea)=> {
                        Frame.Navigate(typeof(DisplayPage), file);
                    };
                    Grid.SetColumn(btn,1);
                    a.Children.Add(txbx);
                    a.Children.Add(btn);
                    Files.Items.Add(a);
                }
            }
        }
/// <summary>
        /// 
        /// </summary>
        public void SaveAsXml() {
            //创建一个XmlDocument对象代表着是一个Xml的文档对象.
            XmlDocument dom = new XmlDocument();
            //创建和添加一条XML的评论
            XmlComment dec = dom.CreateComment("THis is Students Message.");
            dom.AppendChild(dec);

            //添加一个students的元素为Xml文档的根元素
            XmlElement x = dom.CreateElement("studetns");
            dom.AppendChild(x);
            //创建一个Student元素后面再添加到students元素的节点上
            XmlElement x1 = dom.CreateElement("Student");
            //在Student元素里再添加id和name两个元素节点,在节点内还添加了文本内容
            XmlElement x11 = dom.CreateElement("id");
            x11.InnerText = "id001";
            x1.AppendChild(x11);
            XmlElement x12 = dom.CreateElement("name");
            x12.InnerText = "张三";
            x1.AppendChild(x12);
            x.AppendChild(x1);

        }

        private void AppBarButton_Click_1(object sender, RoutedEventArgs e)
        {
            Frame.Navigate(typeof(AddItem));
        }
AddItem.xaml .cs
<Page
    x:Class="App1.AddItem"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App1"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <Grid>
        <Grid x:Name="ContentPanel" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="*"/>
                <RowDefinition Height="*"/>
                <RowDefinition Height="*"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="100*"/>
                <ColumnDefinition Width="346*"/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Column="0" Grid.Row="0" Text="名称:"
                       HorizontalAlignment="Center" VerticalAlignment="Center"/>
            <TextBox Name="nameTxt" Grid.Column="1" Grid.Row="0" MaxHeight="40"/>
            <TextBlock Grid.Column="0" Grid.Row="1" Text="价格:" HorizontalAlignment="Center" VerticalAlignment="Center"/>
            <TextBox x:Name="priceTxt" Grid.Column="1" Grid.Row="1" MaxHeight="40"/>
            <TextBlock Grid.Row="2" Grid.Column="0" Text="数量:"
                       HorizontalAlignment="Center" VerticalAlignment="Center"/>
            <TextBox Name="quanTxt" Grid.Column="1" Grid.Row="2" MaxHeight="40"/>
            <Button x:Name="BtnSave" Content="保存" HorizontalAlignment="Stretch" Grid.Row="3" Grid.ColumnSpan="2" VerticalAlignment="Top" Click="BtnSave_Click"/>
            
        </Grid>
    </Grid>
</Page>
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Runtime.InteropServices.WindowsRuntime;
using Windows.Data.Xml.Dom;
using Windows.Foundation;
using Windows.Foundation.Collections;
using Windows.Storage;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Primitives;
using Windows.UI.Xaml.Data;
using Windows.UI.Xaml.Input;
using Windows.UI.Xaml.Media;
using Windows.UI.Xaml.Navigation;

// https://go.microsoft.com/fwlink/?LinkId=234238 上介绍了“空白页”项模板

namespace App1
{
    /// <summary>
    /// 可用于自身或导航至 Frame 内部的空白页。
    /// </summary>
    public sealed partial class AddItem : Page
    {
        public AddItem()
        {
            this.InitializeComponent();
        }

        private async void BtnSave_Click(object sender, RoutedEventArgs e)
        {
            //获取购物清单的文件夹对象
            StorageFolder storage = await ApplicationData.Current.LocalFolder.GetFolderAsync("ShoppingList");
            //创建一个Xml的对象,示例格式如<Apple pric="23" quantity="3"/>
            XmlDocument _doc= new XmlDocument();
            //使用商品的名称来创建一个xml元素作为根节点
            XmlElement _item=_doc.CreateElement(nameTxt.Text);
            //使用属性来作为信息的标识符,用属性的值来存储相关的信息
            _item.SetAttribute("price", priceTxt.Text);
            _item.SetAttribute("quantity",quanTxt.Text);
            _doc.AppendChild(_item);
            //创建一个应用文件
            StorageFile file = await storage.CreateFileAsync(nameTxt.Text+".xml",CreationCollisionOption.ReplaceExisting);
            //把XML的信息保存到文件中去
            await _doc.SaveToFileAsync(file);
            //调回清单主页
            Frame.GoBack();
        }
    }
}
DisplayPage.xaml .cs
<Page
    x:Class="App1.DisplayPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App1"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <Grid x:Name="ContentPanel" Margin="12,0,12,0">
        <Grid.RowDefinitions>
            <RowDefinition Height="*"/>
            <RowDefinition Height="*"/>
            <RowDefinition Height="*"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*"/>
            <ColumnDefinition Width="*"/>
        </Grid.ColumnDefinitions>
      
            <TextBlock Grid.Column="0" Grid.Row="0" Text="名称:"
                       HorizontalAlignment="Center" VerticalAlignment="Center"/>
            <TextBlock x:Name="nameTxt" Grid.Column="1" Grid.Row="0" FontSize="30"/>

            <TextBlock Grid.Row="1" Text="价格:" HorizontalAlignment="Center" VerticalAlignment="Center"/>
            <TextBlock x:Name="priceTxt" Grid.Column="1" Grid.Row="1" FontSize="30"/>

            <TextBlock Grid.Column="0" Grid.Row="2" Text="数量:"
                       HorizontalAlignment="Center" VerticalAlignment="Center"/>
            <TextBlock Name="quanTxt" Grid.Column="1" Grid.Row="2" FontSize="30"/>
            <TextBlock Name="PageTitle" Grid.Row="3"/>
        </Grid>
  
</Page>
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Runtime.InteropServices.WindowsRuntime;
using Windows.Data.Xml.Dom;
using Windows.Foundation;
using Windows.Foundation.Collections;
using Windows.Storage;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Primitives;
using Windows.UI.Xaml.Data;
using Windows.UI.Xaml.Input;
using Windows.UI.Xaml.Media;
using Windows.UI.Xaml.Navigation;

// https://go.microsoft.com/fwlink/?LinkId=234238 上介绍了“空白页”项模板

namespace App1
{
    /// <summary>
    /// 可用于自身或导航至 Frame 内部的空白页。
    /// </summary>
    public sealed partial class DisplayPage : Page
    {
        public DisplayPage()
        {
            this.InitializeComponent();
        }

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
           //获取上一个清单列表传递过来的参数,该参数是一个StorageFile对象.
           StorageFile file=e.Parameter as StorageFile;
            if (file == null) return;
            //获取文件的名字
            string itemName = file.DisplayName;
            PageTitle.Text = itemName;
            //把应用文件作为一个xml文档加载进来
            XmlDocument doc=await XmlDocument.LoadFromFileAsync(file);
            //获取xml文档的信息
            priceTxt.Text = doc.DocumentElement.Attributes.GetNamedItem("price").NodeValue.ToString();
            quanTxt.Text = doc.DocumentElement.Attributes.GetNamedItem("quantity").NodeValue.ToString();
            nameTxt.Text = itemName;

        }
    }
}

```
6.4.安装包文件数据
可以在应用程序里获取安装包下的文件,就是在应用程序项目中添加的文件.编译文件源代码和资源文件是获取不到的.安装包文件数据和应用文件应用设置所存储的位置是不一样的,如果是保存应用程序业务的相关信息不建议保存到安装包的目录下,安装包文件数据通常是用于内置一些固定的文件数据.
6.41.安装包文件访问
获取安装包的文件可以先通过Windows.ApplicationModel.Package类的InstalledLocation属性来获取安装包的文件夹.
StorageFolder localFolder=Windows.ApplicationModel.Package.Current.InstalledLocation;
``` bash
 <PivotItem Header="ApplicationModelPackage">
                <ScrollViewer>
                    <StackPanel>
                        <Button Content="获取安装包的根目录" x:Name="btnGetFile" Click="btnGetFile_Click" Margin="2"/>
                        <TextBlock Text="文件夹列表:"/>
                        <ListBox x:Name="lbFolder"/>
                        <Button Content="打开选中的文件夹" x:Name="open" Click="open_Click" Margin="2"/>

                        <TextBlock Text="文件列表:"/>
                        <ListBox x:Name="lbFile">
                        </ListBox>
                        <Button Content="在选中文件夹下创建测试文件" x:Name="create" Click="create_Click" Margin="2"/>
                        <Button Content="删除选中的文件" x:Name="delete" Click="delete_Click" Margin="2"/>
                        
                    </StackPanel>
                </ScrollViewer>
            </PivotItem>
/// <summary>
        /// 打开应用程序的根目录
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void btnGetFile_Click(object sender, RoutedEventArgs e)
        {
            //清理文件夹列表
            lbFolder.Items.Clear();
            //获取根目录
            StorageFolder localFolder =Windows.ApplicationModel.Package. Current.InstalledLocation;
            //添加遍历根目录的文件夹到文件夹列表
            foreach (var folder in await localFolder.GetFoldersAsync())
            {
                ListBoxItem item = new ListBoxItem();
                item.Content = "应用程序目录:" + folder.Name;
                item.DataContext = folder;
                lbFolder.Items.Add(item);
            }
            //清理文件列表
            lbFile.Items.Clear();
            //添加遍历根目录的文件到文件列表
            foreach (var file in await localFolder.GetFilesAsync())
            {
                ListBoxItem item3 = new ListBoxItem();
                item3.Content = "文件:" + file.Name;
                item3.DataContext = file;
                lbFile.Items.Add(item3);
            }
        }
        /// <summary>
        /// 打开选中的文件夹
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void open_Click(object sender, RoutedEventArgs e)
        {
            if (lbFolder.SelectedIndex == -1) {
                await new MessageDialog("请选择一个文件夹").ShowAsync();

            }
            else
            {
                ListBoxItem item = lbFolder.SelectedItem as ListBoxItem;
                //获取选中的文件夹
                StorageFolder folder = item.DataContext as StorageFolder;
                //清理文件夹列表
                lbFolder.Items.Clear();
                //添加遍历到的文件夹到文件夹列表
                foreach (var folder2 in await folder.GetFoldersAsync())
                {
                    ListBoxItem item2 = new ListBoxItem();
                    item2.Content = "文件夹:" + folder2.Name;
                    item2.DataContext = folder;
                    lbFolder.Items.Add(item2);
                }
                //清理文件列表
                lbFile.Items.Clear();
                //添加遍历到的文件到文件列表
                foreach (var file in await folder.GetFilesAsync())
                {
                    ListBoxItem item3 = new ListBoxItem();
                    item3.Content = "文件:" + file.Name;
                    item3.DataContext = file;
                    lbFile.Items.Add(item3);
                }

            }
        }
        /// <summary>
        /// 在选中的文件中新建一个文件
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void create_Click(object sender, RoutedEventArgs e)
        {
            if (lbFolder.SelectedIndex == -1)
            {
                await new MessageDialog("请选择一个文件夹").ShowAsync();

            }
            else {
                ListBoxItem item = lbFolder.SelectedItem as ListBoxItem;
                //获取选中的文件夹
                StorageFolder folder=item.DataContext as StorageFolder;
                //在文件夹中创建一个文件
                StorageFile file = await folder.CreateFileAsync(DateTime.Now.Millisecond.ToString()+".txt");
                //添加到文件列表中
                ListBoxItem item3 = new ListBoxItem();
                item3.Content = "文件:" + file.Name;
                item3.DataContext= file;
                lbFile.Items.Add(item3);
                await new MessageDialog("创建文件成功").ShowAsync();
            }
        }

        /// <summary>
        /// 删除选中的文件
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void delete_Click(object sender, RoutedEventArgs e)
        {
            if (lbFile.SelectedIndex == -1) {
                await new MessageDialog("请选择一个文件夹").ShowAsync();
            }
            else
            {
                ListBoxItem item=lbFile.SelectedItem as ListBoxItem;
                //获取选中的文件
                StorageFile file=item.DataContext as StorageFile; 
                //删除文件
                await file.DeleteAsync();
                lbFile.Items.Remove(item);
                await new MessageDialog("删除成功").ShowAsync();
            }
        }

```
6.42 安装包文件的URI方案
应用文件可以通过URI来访问,安装包的文件也一样可以通过URI来访问,不过两者之间的URI方案是有区别的,应用文件使用的是以字符串"ms-appdata:///"开头的URI地址,而安装包使用的是"ms-appx:///".例如获取安装包PackageTest文件夹下的test.xml:
``` bash
var file=await Windows.Storage.StorageFile.GetFileFromApplicationUriAsync(new Uri("ms-appx:///PackageTest/test.xml"));
```
如果是图片文件也可以直接在XAML上给Image控件的Source属性赋值
1.访问存储在本地文件夹中的文件:<Image Source="ms-appdata:///local/images/logo.png"/>
2.访问存储在漫游文件夹中的文件:<Image Source="ms-appdata:///roaming/images/logo.png"/>
3.访问存储在临时文件夹中的文件:<Image Source="ms-appdata:///temp/images/logo.png"/>
4.访问安装包文件夹中的文件:<Image Source="ms-appx:///images/logo.png"/>或者<Image Source="/images/logo.png"/>
下面给出访问应用文件夹和安装包文件夹图片文件的示例:打开示例程序会先将安装包的一个程序图标文件复制到应用文件夹里,然后通过两种不同的URI方案来给Image控件添加图片资源.
``` bash
 <PivotItem Header="AppUri">
                <StackPanel>
                    <TextBlock Text="安装包的图片展示:"/>
                    <Image x:Name="packageImage" Height="200"/>
                    <TextBlock Text="应用存储的图片展示:"/>
                    <Image x:Name="appImage" Height="200"/>
                </StackPanel>
            </PivotItem>
 protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            ApplicationData appData = ApplicationData.Current;
            //将程序包内的文件保存到应用存储中的TemporaryFolder
            StorageFile imgFile = await StorageFile.GetFileFromApplicationUriAsync(new Uri("ms-appx:///Assets/LockScreenLogo.scale-200.png"));
            await imgFile.CopyAsync(appData.TemporaryFolder,imgFile.Name,NameCollisionOption.ReplaceExisting);
            //引用应用存储内的图片文件并显示
            appImage.Source = new BitmapImage(new Uri("ms-appdata:///temp/LockScreenLogo.scale-200.png"));
            //引用程序包内的图片文件并显示
            packageImage.Source = new BitmapImage(new Uri("ms-appx:///Assets/LockScreenLogo.scale-200.png"));

        }
```
#### 图形绘图
7.11.图形中常用的结构.
在图形中经常用到Point,Size,Rect和Color这些数据结构,常用于对图形形状的相关属性赋值
1.Point结构.
Windows.Foundation.Point定义点的位置.
public Point(double x,double y)
2.Size结构
public Size(double width,double height)
3.Rect结构
矩形Rect Windows.Foundation.Rect

1.属性X,Y:矩形结构左上角的x,y坐标.
2.只读属性Left,Top:矩形结构左上角的x,y坐标.
3.只读属性Right和Bottom:矩形结构对象右下角的x,y坐标
4.属性Width,Height,Size矩形结构对象的宽度和高度.
5.构造函数Rect(Point location,Size size) 参数1代表矩形结构左上角点结构,参数2代表矩形宽和高的Size结构.
6.构造函数Rect(Double x,Double y,Double width,Double height):参数依次为矩形左上角x坐标,y坐标,宽和高.
7.方法public void Intersect(Rect rect) 得到调用该方法的矩形结构对象和参数表示的矩形结构的交集

4.Color结构
Windows.UI.Color. a,b,r,g 透明度,蓝色,绿色,红色合成.Color结构支持两种色彩空间sRGB和scRGB.
sRGB用无符号32位数代表一种颜色,红色,绿色,蓝色以及透明度各占一个字节,透明度等于0为完全透明,255为完全不透明.例如完全不透明红色用16进制数表示为#ffff0000,scRGB代表的颜色中的红色,绿色，蓝色以及透明度分别用0-1之间的Sing类型数表示,透明度等于0.0为完全透明,1.0为完全不透明.红色,绿色,蓝色全为0.0 表示黑色,全为1.0表示白色.例如不透明红色表示为sc#1.0,1.0,0.0,0.0,其常用属性和方法如下:
public static Color FromArgb(byte a,byte r,byte g,byte b):sRGB颜色.
7.12.画图相关的类
有两组类可用于定义空间的区域:Shape和Geometry.主要区别是:Shape拥有与之关联的画笔并可以呈现到屏幕,但Geometry只定义空间的区域并且不会呈现.可以认为Shape是由Geometry定义的有边界的UIElement,
Shape类 有 画线段类Line,Rectangle类,Ellipse,多条线段类Polyline,画由多条线段组成的闭合图形类Polygon,Path.Windows.UI.Xaml.Shapes.Shape
1.Fill 填充的画刷 Winodws.UI.Xaml.Media.Brush类型
2.Stroke 表示笔触 图形的边界颜色 Windows.UI.Xaml.Media.Brush类型
3.StrokeThickness 笔触尺寸.可以理解为图形边界大小.
4.Stretch None Fill Uniform UniformToFill 
5.StrokeDashArray 表示虚线和间隙值的集合,用于画虚线,StrokeDashArray参数采用S[,G][,S*,G**]*的形式,其中S表示笔画的长度值,G表示间隙的长度值,如果忽略了G,则间隙长度与前一个笔画长度相同,例如线宽=1，"2"表示2个像素的实线和2个像素的空白组成的虚线,"3,2"表示3个像素实线和2个像素空白组成的虚线,
"2,2,3,2"表示2个像素的实线和2个像素的空白+3个像素的实线和2个像素的空白(如此反复)组成的虚线.
实际实线和空白间隔像素还受线宽的影响.
6.StrokeDashCap:表示虚线两端(线帽)的类型,用于画虚线(Windows.UI.Xaml.Media.PenLineCap枚举类型)
PenLineCap是描述直线或线段末端的形状,枚举值Flat表示一个未超出直线上最后一点的线帽,等同于无线帽,
Square表示一个高度等于直线粗细,长度等于直线粗细一半的矩形,Round表示一个直径等于直线粗细的半圆形;Triangle表示一个底边长度等于直线粗细的等腰直角三角形.
7.StrokeStartLineCap:虚线起始端(线帽)的类型(Windows.UI.Xaml.Media.PenLineCap枚举类型)
8.StrokeEndLineCap:虚线终结端(线帽)的类型
9.StrokeDashOffset:虚线的起始位置,用于画虚线,从虚线的起始端的StrokeDashOffset距离处开始描绘虚线.
10.StrokeLineJoin:图形连接点处的连接类型(Windows.UI.Xaml.Media.PenLineJoin枚举类型)
PenLineJoin是描述连接两条线或线段的形状的枚举类型---Miter表示线条连接使用常规角顶点;Bevel表示线条连接使用斜角顶点.Round表示线条连接使用圆角顶点.
11.StrokeMiterLimit:斜接长度与StrokeThickness/2的比值.默认值为10,最小值为1.
7.13.基础的图形形状
1.Line线段
控件Line来画线段,X1 Y1 起点 X2 Y2 终点
``` bash
<Line X1="0" Y1="0" X2="100" Y2="100" Stroke="Black" StrokeThickness="10"/>
```
2.Rectangle矩形
控件Rectangle可用来画各种矩形,属性Width,Height,RadiusX和RadiusY分别是矩形的宽,高,圆角矩形的圆角x轴半径和y轴半径.x轴半径要小于等于Width二分之一,y轴半径要小于等于height二分之一,当二者都等于二分之一,则圆形变为圆或椭圆.
<Rectangle Width="200" height="100" Fill="Blue" RadiusX="20" RadiusY="20"/>
3.Ellipse椭圆
控件Ellipse画椭圆时,如果Width=height,则为圆.
<Ellipse Stroke="Blue" Fill="Red" Width="100" height="100" StrokeThickness="8"/>
4.Polyline开放多边形和Polygon封闭多边形
Polyline类属性Ponints是点结构数组,将数组元素Ponints[0]和Ponints[1],Ponints[1]和Ponints[2],等点连接为多条线段 设置类Polyline属性IsClose=true 也能完成Polygon类相同功能.
``` bash
<Polyline Points="10,110 60,10 110,110" Stroke="Red" StrokeThickness="4"/>
<Polygon Points="10,110 60,10,110,110" Stroke="Red" StrokeThickness="4"/>
```
实现更高的灵活化,采用Line去实现画图的功能.
``` bash
<PivotItem Header="Line">
                <Canvas x:Name="ContentPanelCanvas" Background="Transparent">
                
                </Canvas>
            </PivotItem>

 //注册画图的指针移动事件,用来捕获手指在设备屏幕上的点
            this.ContentPanelCanvas.PointerMoved += ContentPanelCanvas_PointerMoved;
            //注册指针按下事件,用来记录第一个触摸点
            this.ContentPanelCanvas.PointerPressed += ContentPanelCanvas_PointerPressed;
    /// <summary>
        /// 指针按下事件
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        /// <exception cref="NotImplementedException"></exception>
        private void ContentPanelCanvas_PointerPressed(object sender, PointerRoutedEventArgs e)
        {
            currentPoint = e.GetCurrentPoint(ContentPanelCanvas).Position;
            oldPoint= currentPoint;

        }

        private void ContentPanelCanvas_PointerMoved(object sender, PointerRoutedEventArgs e)
        {
            ///获取相对于画布控件的点坐标
            currentPoint=e.GetCurrentPoint(ContentPanelCanvas).Position;
            //根据上一个画图的点和当前的画图的点新建一条线段
            Line line = new Line() { X1=currentPoint.X,Y1=currentPoint.Y,X2=oldPoint.X,Y2=oldPoint.Y};
            //设置线段的相关属性
            line.Stroke = new SolidColorBrush(Colors.Red);
            line.StrokeThickness = 5;
            line.StrokeLineJoin = PenLineJoin.Round;
            line.StrokeStartLineCap= PenLineCap.Round;
            line.StrokeEndLineCap= PenLineCap.Round;
            //把线段添加到画布面板上
            this.ContentPanelCanvas.Children.Add(line);
            //把当前的画图的点作为上一个画图的点
            oldPoint= currentPoint;


        }
```
7.2 Path图形
7.21.两种Path图形的创建方法
最重要的属性是Data,Data的数据类型是Geometry(几何图形),正是使用这个属性将一些基本的线段拼接起来,形成复杂的图形.Data属性赋值的方法有两种,一种是使用Geometry图形绘制的标准语法;另一种是专门用于绘制几何图形的"路径标记语法".用Geometry图形实现的Path图形显得更加直观.用路径标记语法则显得更加简洁.
``` bash
<Path Fill="Gold" Stroke="Black" StrokeThickness="1">
	<Path.Data>
		<RectangleGeometry Rect="0,0,100,100"/>
	</Path.Data>
</Path>
<Path Data="M0,L100,0 L100,100 L0,100 z" Fill="Gold" Height="100" Strenth="Fill" Width="100"/>
```
7.22.使用简单的几何图形来创建Path
使用Geometry来创建Path图形,是非常直观的语法,它最大的优点是不仅仅可以通过XAML实现,还可以使用C#代码创建,所以如果要动态地改变Path的形状就需要使用Geometry来创建Path图形.
Path的Data属性是Geometry类,但是Geometry类是一个抽象类,所以不可能在XAML中直接使用<Geometry>标签.可以使用Geometry的子类.Geometry的子类包括以下几种
1.LineGeometry:直线几何图形
2.RectangleGeometry:矩形几何图形
3.EllipseGeometry:椭圆几何图形
4.PathGeometry:路径几何图形,PathGeometry是Geometry中最灵活的,可以绘制任意的2D几何图形
5.GeometryGroup:由多个基本几何图形组合在一起,形成几何图形组.
定义空间的区域有Shape和Geometry两种类型.Shape类型就是前面所讲的Line,Rectangle,Ellipse类,
Geometry类型不仅仅可以用在Path的Data属性上,还可以用在UIElement的Clip属性上.
Geometry对象可以分为3个类别:简单几何图形,路径几何图形以及复合几何图形.
简单几何图形类包括LineGeometry,RectangleGeometry和EllipseGeometry.用于创建基本的几何形状,例如直线,矩形和圆.
1.LineGeometry 通过指定直线的起点和终点来定义.
2.RectangleGeometry 通过使用Rect结构来定义,该机构指定矩形的相对位置,高度和宽度.
3.EllipseGeometry 通过中心点,x半径和y半径来定义.
``` bash
 <PivotItem Header="Geometry">
                <StackPanel>
                    <!--直线-->
                    <Path Stroke="Red" StrokeThickness="2">
                        <Path.Data>
                            <LineGeometry StartPoint="0,0" EndPoint="400,20"/>
                        </Path.Data>
                    </Path>
                    <!--矩形路径-->
                    <Path Fill="Red">
                        <Path.Data>
                            <RectangleGeometry Rect="20,20,400,50"/>
                        </Path.Data>
                    </Path>
                    <!--椭圆路径-->
                    <Path Fill="Red">
                        <Path.Data>
                            <!--<GeometryGroup组合>-->
                            <GeometryGroup FillRule="EvenOdd">
                                <RectangleGeometry Rect="80,50,200,100"/>
                                <EllipseGeometry Center="300,100" RadiusX="80" RadiusY="60">
                                    
                                </EllipseGeometry>
                            </GeometryGroup>
                        </Path.Data>
                    </Path>

                    <Path Fill="Red" StrokeThickness="3">
                        <Path.Data>
                            <!--<GeometryGroup组合>-->
                            <GeometryGroup FillRule="Nonzero">
                                <RectangleGeometry Rect="80,50,200,100"/>
                                <EllipseGeometry Center="300,100" RadiusX="80" RadiusY="60"/>
                            </GeometryGroup>
                        </Path.Data>
                    </Path>
                </StackPanel>
            </PivotItem>
        </Pivot>
```
7.23.使用PathGeometry来创建Path
PathGeometry是Geometry里最灵活的几何图形,相当于Shape里面的Path一样,PathGeometry的核心是PathGeometry对象的集合,这些对象之所以这样命名是因为每个图形都描绘PathGeometry中的一个离散状态,每个PathFigure自身又由一个或多个PathSegment对象组成,每个这样的对象均描绘图形的一条线段.
PathGeometry所支持的Segment
ArcSegment 在两个点之间创建一条椭圆弧线
BezierSegment 在两个点之间创建一条三次方贝塞尔曲线
LineSegment 在两个点之间创建一条直线
PolyBezierSegment 创建一系列三次方贝塞尔曲线.
PolyQuadraticBezierSegment 创建一系列二次方贝塞尔曲线
PolyLineSegment 创建一系列直线
QuadraticBezierSegment 创建一条二次贝塞尔曲线
PathGeometry的Figures属性可以容纳PathFigure对象.而PathFigure对象的Segments属性又可以容纳各种线段用来组合成复杂的图形.
1.LineSegment和PolylineSegment
LineSegment类表示在两个点之间绘制一条线,它可能是Path数据的PathFigure的一部分.使用PathFigure对象通过LineSegment对象和其他线段来创建复合形状.LineSegment类不包含用于直线起点的属性,直线的起点是前一条线段的终点,如果不存在其他线段,则为PathFigure的StartPoint;其他的Segment也是一样的规则.
可以使用Line生成线条的简单形状,LineSegment用于在更复杂的几何组中绘制线.
``` bash
<Path Stroke="Red" StrokeThickness="5">
                    <Path.Data>
                        <PathGeometry>
                            <PathFigure StartPoint="10,20">
                                <PathFigure.Segments>
                                    <LineSegment Point="100,130"/>
                                </PathFigure.Segments>
                            </PathFigure>
                        </PathGeometry>
                    </Path.Data>
                </Path>
```
PolyLineSegment类表示由PointCollection定义的线段集合,每个Point指定线段的终点.
PolyLineSegment在Xaml上语法示例如下,省略掉部分Path的代码与上文相同
``` bash
<PolyLineSegment Points="50,100,50,150"/>
```
2.ArcSegment绘制圆弧.
Point属性指明圆弧连接的终点,Size属性表示完整椭圆的横轴和纵轴半径.RotationAngle属性指明圆弧母椭圆的旋转角度,SweepDirection属性指明圆弧是顺时针方向还是逆时针方向,IsLargeArc属性指明是否使用大弧去连接,如果椭圆上的两个点的位置不对称,这两点间的圆弧就会分为大弧和小弧.
``` bash
<Path Stroke="Red" StrokeThickness="5">
                    <Path.Data>
                        <PathGeometry>
                            <PathGeometry.Figures>
                                <PathFigure StartPoint="10,50">
                                    <PathFigure.Segments>
                                        <ArcSegment Size="50,50" RotationAngle="45" IsLargeArc="True" SweepDirection="Clockwise" Point="200,100">
                                            
                                        </ArcSegment>
                                    </PathFigure.Segments>
                                </PathFigure>
                            </PathGeometry.Figures>
                        </PathGeometry>
                    </Path.Data>
                </Path>
```
3.BezierSegment,PolyBezierSegment,PolyQuadraticBezierSegment和QuadraticBezierSegment
一条三次方贝塞尔曲线由4个点定义:一个起点,一个终点,和两个控制点(Point1和Point2).三次方贝塞尔曲线的两个控制点作用像磁铁一样,朝着自身的方向吸引本应为直线的部分,从而形成一条曲线.第一个控制点Point1影响曲线的开始部分;第二个控制点Point2影响曲线的结束部分.注意,曲线不一定必须通过两个控制点之一.每个控制点将直线的一部分朝着自己的方向移动,但不能通过自身,PolyBezierSegment通过将Points属性设置为点集合来指定一条或多条三次方贝塞尔曲线.PolyBezierSegment实质上可以有无限个控制点,这些点和终点的值由Points属性值提供.
QuadraticBezierSegment表示二次方贝塞尔曲线,与BezierSegment类似,只是控制点由两个变成了一个.也就是说QuadraticBezierSegment由3个点决定:起点(即前一个线段的终点或PathFigure的StartPoint),终点(Point2属性,即曲线的终止位置)和控制点(Point1属性)
7.24.使用路径标记语法创建Path
路径标记语法 就是各种线段的简记法.<LineSegment point="150,5"/>可以简写为"L 150,5"
还增加了一些更实用的绘图命令 H用来绘制水平线,"H 180"就是指从当前点画一条水平直线,终点的横坐标是180.(不需要考虑纵坐标,纵坐标和当前点一致.) 类似的还有V命令,用来划竖直直线.
使用路径标记语法绘图一般分三步,移动至起点---绘图---闭合图形 移动M 绘图 L ,H,V,A,C,Q等.如果图形是闭合的,需要使用闭合命令Z,这样最后一条线段终点与第一条线段的起点就会连接上一条直线段.
在路径标记语法中使用两个Double类型的数值来表示一个点.第一个值表示的是横坐标(记作X),第二个值表示纵坐标(记作y),两个数字可以使用逗号分隔(x,y),也可以使用空格分隔. 
``` bash
 <Path Stroke="Red" StrokeThickness="3" Data="M 100,200 C 100,25 400,350 400,175 H 280"/>
```
上面Path创建了由一条贝塞尔曲线线段和一条线段组成的Path.Data字符串以'move'命令开头(M),建立起点,大写的M表示新的当前点的绝对位置.小写的m表示相对位置.第一个线段是一个三次方贝塞尔曲线,该曲线从(100,200)开始,在(400,175)结束,使用(100,25)和(400,350)这两个控制点绘制.此线段由Data字符串中的C命令指示.同样,大写的C表示绝对路径,小写的c代表相对路径.第二个线段以绝对水平线命令H开头,它指定绘制一条从前面的子路径的终结点(400,175)到新终结点(280,175)的直线.由于他是一个水平线命令,因此指定的值是x坐标.
路径标记语法
移动指令 M  M x,y或m x,y 大写M x,y是绝对值;小写的m指示x,y是相对于上一个点的偏移量, 0,0表示不存在偏移.在move命令之后列出多个点时,即使指定的是线条命令,也将绘制出连接这些点的线.
绘制指令 通过使用一个大写或小写字母输入各命令,大写表示绝对值,小写表示相对值,线段的控制点是相对于上一线段终点而言的,依次输入多个同一类型的命令时,可以省略重复的命令项,例如,L 100,200 300,400 等同于L 100,200 L 300,400
	直线 Line L 格式:L结束点坐标 或I结束点坐标. L 100
	水平直线 Horizontal line H H x值或h x值(x为System.Double类型的值) 绘制从当前点到指定x坐标的水平直线. H 100
	垂直直线 Vertical line V V y或v y值 绘制从当前点到指定y坐标的垂直直线. V1 100
	三次方贝塞尔曲线 Cubic Bezier curve C C第一控制点 第二控制点 结束点 通过指定两个控制点来绘制由当前点到指定结束点间的三次方程贝塞尔曲线. C 100,200 200,400 300,200   100,200为第一控制点
	二次方程式贝塞尔曲线 Quadratic Bezier curve Q Q控制点结束点或q控制点结束点 通过指定的一个控制点来绘制由当前点到指定结束点间的二次方程贝塞尔曲线. q 100,200 300,200  100,200控制点 300，200 结束点
	平滑三次方程式贝塞尔曲线 Smooth cubic Bezier curve (S) S控制点结束点或s控制点结束点 通过一个指定点来"平滑"控制当前点到指定点的贝塞尔曲线 如 S 100,200 200,300
	平滑二次方程式贝塞尔曲线 smooth quadratic bezier curver T  T控制点 结束点  T 100,200 200,300
	椭圆圆弧:elliptical Arc A A尺寸圆弧旋转角度值优势弧的标记正负角度标记结点 在当前点与指定结束点间绘制圆弧. 
	尺寸 Size 指定椭圆圆弧X,Y方向上的半径值. 
	旋转角度 rotationAngle System.Double类型. 圆弧旋转角度值
	优势弧的标记(isLargeArcFlag) 是否为优势弧,如果弧的角度大于等于180°,则设为1,否则为0
	正负角度标记(sweepDirectionFlag) 当正角方向绘制时设为1 否则为0. 
	结束点 endPoint  例如:A 5,5 0 0 1 10,10
关闭指令 可选 用Z或z表示.用以将图形的首,尾点用直线连接,以形成一个封闭的区域
填充规则 fileRule 如果省略此命令,则路径使用默认行为,即EvenOdd.如果指定此命令,则必须将其置于最前面. 
	EvenOdd填充规则 F0指定EvenOdd填充规则.EvenOdd确定一个点是否位于填充区域内的规则方法:从该点沿任意方向画一条无限长的射线,然后计算该射线在给定形状中因交叉而形成的路径段数.如果该数为奇数，则点在内部;如果为偶数,则点在外部
	Nonzero填充规则 F1指定Nonzero填充规则 Nonzero确定一个点是否位于路径填充区域内的的规则方法:从该点沿任意方向画一条无限长的射线,然后检查形状段与该射线的交点,从0开始计数,每当线段从左到右穿过该射线时加1,而每当路径段从左到右穿过该射线时加1,而每当路径段从右向左穿过该射线-1.计算交点的数目后,如果结果为0,则说明该点位于路径外部,否则,它位于路径内部.
7.25.使用Path实现自定义图形
布局面板通过重载FrameworkElement类的ArrangeOverride方法对子对象进行排列,从Path类派生的图形类一样也可以通过重载ArrangeOverride方法对图形进行初始化.在初始化逻辑生成适用于自定义形状的几何图形,随后再将其设置赋值给Path.Data属性,可以充分利用Width和Height等这些Path自带的属性作为自定义图形的相关参数,当然如果需要额外的参数,也是可以通过实现自定义属性进行传递.
``` bash
   <Grid x:Name="HexagonContentPanel" Margin="12,0,12,0">
                    <controls:Hexagon Height="300" Width="300" Stroke="Yellow" StrokeThickness="8" Fill="Red"/>
                </Grid>

 public class Hexagon : Path
    {
        private double lastWidth = 0;
        private double lastHeight = 0;
        private PathFigure figure;
        public Hexagon()
        {
            CreateDataPath(0,0);
        }

        private void CreateDataPath(double width,double height)
        {
            //把高度和宽度先减去边的宽度
            height -= this.StrokeThickness;
            width-= this.StrokeThickness;
            //用来跳出布局的循环,因为只创建一次
            if (lastWidth == width && lastHeight == height)
                return;
            lastWidth= width;
            lastHeight= height;
            PathGeometry geometry= new PathGeometry();
            figure= new PathFigure();
            //取左上角的点为第一个点
            figure.StartPoint=new Windows.Foundation.Point(0.25*width,0);
            //算出每个顶点的坐标然后创建线段连接起来
            AddPoint(0.75*width,0);
            AddPoint(width,0.5*height);
            AddPoint(0.75*width,height);
            AddPoint(0.25*width,height);
            AddPoint(0,0.5*height);
            figure.IsClosed= true;
            geometry.Figures.Add(figure);
            this.Data = geometry;
        }

        private void AddPoint(double x, double y)
        {
           LineSegment segment= new LineSegment();
            segment.Point=new Point(x+0.5*StrokeThickness,y+0.5*StrokeThickness);
            figure.Segments.Add(segment);
        }
        protected override Size MeasureOverride(Size availableSize)
        {
            return availableSize;
        }

        protected override Size ArrangeOverride(Size finalSize)
        {
            //初始化图形
            CreateDataPath(finalSize.Width,finalSize.Height);
            return finalSize;
        }


    }
```
7.26.利用Expression Blend工具创建Path图形
1.使用笔或者铅笔工具画出Path图形
打开Expression Blend工具,可以找到左边图标工具栏上的笔或铅笔
2.把相关的图形转换成Path图形.
在Blend里面除了基本图形外,还封装了很多其他的图形,可以直接把这些图形转换为Path图形.转换的方法是在图形上面,右键——>路径——>转换为路径,这时候就把相关的图形转换为Path绘图的方式.
3.通过合并图形的方式生成Path图形
合并图形的方式是指在Blend里面创建了多个图形,按住Ctrl键把要合并的图形选中,然后右键--->合并--->相并/拆分/相交/相减/排除重叠.
4.导入设计的源文件
在Blend里可以导入Illustrator文件/FXG文件/Photoshop文件,把这些文件转换为XAML编码,同时也会把里面相关的图形转换为Path图形,这是Blend工具提供的一个非常有用的一个功能,通常设计师会采用Phoneshop设计图片,直接把设计好的Photoshop文件导入就可以把文件转成为XAML编码
7.3画刷
使用XAML时,在控件的Background,Foreground和Fill属性中简单的输入颜色名,应用程序运行时就会将字符串值转换为有效的颜色资源对应的画刷.
7.31.SolidColorBrush画刷
SolidColorBrush画刷主要用来填充单色形状或控件,可以直接通过颜色进行创建,在Xaml中直接输入颜色名使用的就是SolidColorBrush画刷,可以通过直接使用Colors里面的颜色值进行赋值.
``` bash
<Rectangle width="100" height="100" Fill="#FFFF0000"/>
```
7.32.LinearGradientBrush画刷
线性渐变画刷(LinearGradientBrush)用来填充一个复合渐变色到一个元素中,并且可以任意搭配两种或两种以上的颜色,重要的属性有倾斜点(GradientStop),渐变颜色(Color),起始坐标点(StartPoint),结束坐标点(EndPoint).
``` bash
<Path Canvas.Left="15" Canvas.Top="50" Stroke="Black" Data="M 0,0 A 15,5 180 1 1 200,0 L 200,100 L 300,100 L 300,200 A 15,5 180 1 1 100,200 L 100,100 L 0,100 Z">
	<Path.Fill>
		<LinearGradientBrush StartPoint="0,0" EndPoint="1,0">
			<GradientStop Offset="0" Color="DarkBlue"/>
			<GradientStop Offset="1" Color="LightBlue"/>
		</LinearGradientBrush>
	</Path.Fill>
</Path>
```
7.33.ImageBrush画刷
ImageBrush画刷使用图像绘制一个区域,由其ImageSource属性指定的JPEG或PNG图像绘制区域,默认情况下,ImageBrush会将其图像拉伸以完全充满要绘制的区域,如果绘制的区域和该图像的长宽比不同,则可能会扭曲该图像.可以通过将Stretch属性从默认值Fill更改为None,Uniform或者UniformToFill来更改此现象.
``` bash
<Ellipse Height="180" Width="180" Margin="50,0,0,0">
	<Ellipse.Fill>
		<ImageBrush ImageSource="Assets/ApplicationIcon.png" Stretch="Fill"/>
	</Ellipse.Fill>
</Ellipse>
```
通常会利用ImageBrush来做一些特别的遮罩效果,
7.4 图形裁剪
Geometry类型还有一个用途就是可以用在UIElement的Clip属性上对从UIElement派生的控件进行图形裁剪,裁剪出来的形状就是Geometry类型所构成的图形.Geometry类型在UIElement的Clip属性上的语法和在Path的Data属性上的语法完全一样.
7.41.使用几何图形进行裁剪
裁剪图像时是指仅把图像的某个局部区域的图像显示出来.可以通过使用UIElement的Clip属性来设置图像裁剪的区域,Clip属性值为Geometry类型的派生类,可以取值为RectangleGeometry类型,这就意味着可以从图像中裁切掉矩形的几何形状.
``` bash
<Image Source="myImage.jpg" Width="200" Height="150">
	<Image.Clip>
		<RectangleGeometry Rect="0,0,50,50"/>
	</Image.Clip>
</Image>
```
因为Clip属性是UIElement的属性,所以不仅仅只有Image控件可以使用其来裁剪图形,所有的UI对象都可以使用Clip属性对UI进行裁剪.
7.42.对布局区域进行剪裁
使用布局面板来布局一些子控件,实际上这些子控件所在的位置可以超出布局面板的范围,实现一个布局面板,让面板里的子控件都在面板的范围里,超出面板就不可见.可以利用Clip属性去实现,因为Clip属性是UIElement类的属性,也就是说所有的XAML控件都可以使用这个属性去进行图形裁剪.
``` bash
  <Grid x:Name="ClipContentPanel" Margin="12,0,12,0">
                    <Canvas Background="White" Margin="20" controls:Clip.ToBounds="True">
                        <Ellipse Fill="Red" Canvas.Top="-10" Canvas.Left="-10" Width="200" Height="200"/>
                    </Canvas>
                </Grid>

public class Clip 
    {


        public static bool GetToBounds(DependencyObject obj)
        {
            return (bool)obj.GetValue(ToBoundsProperty);
        }

        public static void SetToBounds(DependencyObject obj, bool value)
        {
            obj.SetValue(ToBoundsProperty, value);
        }

        // Using a DependencyProperty as the backing store for ToBounds.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty ToBoundsProperty =
            DependencyProperty.RegisterAttached("ToBounds", typeof(bool), typeof(Clip), new PropertyMetadata(false,OnToBoundsPropertyChanged));
        /// <summary>
        /// 定义属性改变事件
        /// </summary>
        /// <param name="d"></param>
        /// <param name="e"></param>
        /// <exception cref="NotImplementedException"></exception>
        private static void OnToBoundsPropertyChanged(DependencyObject d, DependencyPropertyChangedEventArgs e)
        {
            FrameworkElement fe = d as FrameworkElement;
            if (fe != null)
            {
                //裁剪图形
                ClipToBounds(fe);
                //当对象加载或者大小改变时都需要重新处理边界
                fe.Loaded += Fe_Loaded;
                fe.SizeChanged += Fe_SizeChanged;
            }
        }

        private static void Fe_SizeChanged(object sender, SizeChangedEventArgs e)
        {
            ClipToBounds(sender as FrameworkElement);
        }

        private static void Fe_Loaded(object sender, RoutedEventArgs e)
        {
            ClipToBounds(sender as FrameworkElement);
        }

        private static void ClipToBounds(FrameworkElement fe)
        {
            //如果ToBounds设置为true则进行裁剪
            if (GetToBounds(fe))
            {
                //使用布局面板的实际高度和宽度来创建一个RectangleGeometry赋值给Clip属性
                fe.Clip = new Windows.UI.Xaml.Media.RectangleGeometry()
                {
                    Rect = new Windows.Foundation.Rect(0, 0, fe.ActualWidth, fe.ActualHeight)
                };
            }
            else
            {
                fe.Clip = null;
            }
        }
    }
```
7.5.使用位图编程
Image控件用于显示图片,在Win10中将一张图片显示出来可以添加一个Image控件,然后在控件中设置图片的路径.使用Image控件显示图片语法如下:
``` bash
<Image Source="myPicture.png"/>
Image myImage=new Image();
myImage.Source=new BitmapImage(new Uri("ms-appx:///myPicture.jpg",UriKind.Absolute));
```
Source属性用于指定要显示的图像位置,在路径中,使用ms-appx URI方案名称编写安装文件夹地址:ms-appx:///，表示指向于安装包文件的地址.
7.51 拉伸图像
如果没有设置Image的Width和Height值,使用图像自然尺寸显示,Stretch:None,Uniform,Uniform	ToFill.
7.52.使用RenderTargetBitmap类生成图片
RenderTargetBitmap类可以将可视化对象转换为位图,也就是说可以将任意的UIElement以位图的形式呈现,在实现的编程中通常利用RenderTartgetBitmap类对UI界面进行截图操作.
使用RenderTargetBitmap类生成图片一般有两种用途,一种是直接把生成的图片在当前的页面上进行展示,还有一种是把生成的图片当作文件存储起来,或者通过某种分享方向把图片文件分享出去.
使用RenderTargetBitmap类生成图片操作主要是依赖于RenderTargetBitmap类的RenderAsync方法.
RenderAsync方法有两个重载;RenderAsync(UIElement)和RenderAsync(UIElement,Int32,Int32),可在后者处指定要与源可视化树的自然大小不同的所需图像源尺寸.RenderAsync方法设计为异步方法,因此无法保证与UI源进行精确的框架同步,但大多数情况下都足够及时.由于RenderTargetBitmap是ImageSource的子类,因此,可以将其用作Image元素或ImageBrush画笔的图像源.
``` bash
 <PivotItem Header="RenderTargetBitmap">
                <!--注册PointerReleased事件用于捕获屏幕的单击操作,并在时间处理程序中生成图片.-->
                <Grid x:Name="root" Background="{ThemeResource ApplicationPageBackgroundThemeBrush}" PointerReleased="root_PointerReleased">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="*"/>
                    </Grid.RowDefinitions>
                    <StackPanel x:Name="TitlePanel" Margin="12,35,0,28">
                        <TextBlock Text="我的应用程序" FontSize="20"/>
                        <TextBlock Text="点击截屏" FontSize="60"/>
                    </StackPanel>
                    <Grid x:Name="RenderTargetBitmapContentPanel" Grid.Row="1" Margin="12,0,12,0">
                    <!--该图片控件用于展示截图图片效果-->
                        <Image x:Name="img"/>
                    </Grid>
                </Grid>
            </PivotItem>
 /// <summary>
        /// 指针释放的事件处理程序
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void root_PointerReleased(object sender, PointerRoutedEventArgs e)
        {
            //创建一个RenderTargetBitmap对象,对界面中的Grid控件root生成图片
            RenderTargetBitmap bitmap = new RenderTargetBitmap();
            await bitmap.RenderAsync(root);
            //把图片展现出来
            img.Source=bitmap;
        }
```
7.53.存储生成的图片文件
在调用RenderAsync方法时会初始化RenderTargetBitmap类的对象,但是RenderTargetBitmap类的对象本身并不能作为图片来进行存储,要生成图片文件需要获取到图片的二进制数据,如果想要获取DataTransferManager操作 如共享协定交换的图像,或者想要使用Windows.Graphics.Imaging Api将效果应用到图像上或对图像进行转码,就需要用到像素数据,如果访问RenderTargetBitmap的pIxels数据,需要在用RenderAsync方法将UIElement定义为RenderTargetBitmap后m在调用RenderTargetBitmpa的GetPixelsAsync方法来获取其Pixels数据,该方法返回值是IBuffer类型,里面存储的是二进制位图数,这个IBuffer可以转换成一个Byte数组,数组里面的数据以BGRA8格式存储.
以下代码示例如何从一个RenderTargetBitmap对象中获得以Byte数组类型存储的像素数.需要特别注意的是IBuffer实例调用的ToArray方法是一个扩展方法,需要在项目中加入System.Runtime.InteropServices.WindowsRuntime这个命名空间.
``` bash
var bitmap=new RenderTargetBitmap();
await bitmap.RenderAsync(elementToRender);
IBuffer pixelBuffer=await bitmap.GetPixelsAsync();
byte[] pixels=pixelBuffer.ToArray();
```
在获取到了图像的二进制数据之后,如果要把二进制的数据生成图片文件,需要使用到BitmapEncoder类.BitmapEncoder类包含创建,编辑和保存图像的各种方法.创建图片文件首先需要调用BitmapEncoder类CreateAsync方法,使用文件的流创建一个BitmapEncoder对象,然后再使用BitmapEncoder类的SetPixelData设置图像有关帧的像素数据.SetPixelData的方法参数如下:
``` bash
SetPixelData(BitmapPixelFormat pixelFormat,BitmapAlphaMode alphaMode,uint width,uint height,double dpiX,double dpiY,byte[] pixels)
```
其中,pixelFormat表示像素数据的像素格式,alphaMode表示像素数据的alpha模式,width表示像素数据的宽度(以像素为单位),height表示像素数据的高度(以像素为单位);dpiX表示像素数据的水平分辨率(以每英寸点数为单位);pixels表示像素数据.该方法是同步的,因为直到调用FlushAsync,GoToNextFrameAsync或GoToNextFrameAsync(IIterable(IkeyValuePair))才会提交数据.该方法将所有像素数据视为sRGB颜色空间中的像素数据.
保存截图文件的示例
``` bash
            <PivotItem Header="SaveBitmapEncoder">
                <Grid x:Name="root1" Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
                    <Grid x:Name="BitmapEncoderContentPanel" Margin="12,0,12,0">
                        <StackPanel>
                            <Button x:Name="bt_save1" Content="存储生成的图片" Click="bt_save_Click_2"/>
                            <Button x:Name="bt_show"
                                    Content="展示存储的图片"
                                    Click="bt_show_Click"/>
                            <ScrollViewer BorderBrush="Red" BorderThickness="2" Height="350">
                                <Image x:Name="img1"/>
                                
                            </ScrollViewer>
                        </StackPanel>
                    </Grid>
                </Grid>
            </PivotItem>
 /// <summary>
        /// 按钮事件生成图片并保存到程序的存储里
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private async void bt_save_Click_2(object sender, RoutedEventArgs e)
        {
            //生成RenderTargetBitmap对象.
            RenderTargetBitmap bitmapbitmap = new RenderTargetBitmap();
            await bitmapbitmap.RenderAsync(root1);
            //获取图像的二进制数据
            var pixelBuffer=await bitmapbitmap.GetPixelsAsync();
            //创建程序文件存储
            IStorageFolder applicationFolder=ApplicationData.Current.LocalFolder;
            IStorageFile saveFile = await applicationFolder.CreateFileAsync("snapshot.png",CreationCollisionOption.OpenIfExists);
            //把图片的二进制数据写入文件存储
            using (var fileStream=await saveFile.OpenAsync(FileAccessMode.ReadWrite))
            {
                var encoder = await BitmapEncoder.CreateAsync(BitmapEncoder.PngEncoderId,fileStream);
                encoder.SetPixelData(
                    BitmapPixelFormat.Bgra8,
                    BitmapAlphaMode.Ignore,
                    (uint)bitmapbitmap.PixelWidth,
                    (uint)bitmapbitmap.PixelHeight,
                    DisplayInformation.GetForCurrentView().LogicalDpi,
                    DisplayInformation.GetForCurrentView().LogicalDpi,
                    pixelBuffer.ToArray());
                await encoder.FlushAsync();
            }
        }
        /// <summary>
        /// 展示程序存储图片的按钮事件
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void bt_show_Click(object sender, RoutedEventArgs e)
        {
            // “ms-appdata:///local”表示是程序存储的根目录
            BitmapImage bitmapImage=new BitmapImage(new Uri("ms-appdata:///local/snapshot.png",UriKind.Absolute));
            img1.Source= bitmapImage;
        }
```
