# QT IFW


参考[使用 Qt Installer Framework 制作安装包与程序自动升级](https://pressc.cn/1041.html)
参考[Qt Installer Framework 使用说明](https://www.cnblogs.com/oloroso/p/6775220.html)

1. Qt Installer Framework 提供了一组工具和实用工具来创建安装程序，并在不重写源代码的情况下将它们部署到所有受支持的桌面 Qt 平台上，它可以运行在 Linux、Microsoft Windows、and OS X 平台上。

## 1. 目录结构

```yml
bin
    binarycreator.exe   用于生成安装程序
    repogen.exe         用于生成升级仓库
```

## 2. 创建安装程序

### 2.1. 创建目录

1. 创建一个`Package`目录，包含所有配置文件和可安装软件包；
2. 在`Package`目录中包含 `config` 和 `packages` 两个子目录;
3. 在 `packages` 可以放置不同的程序组件，目录名一般以 `domain-like identifier` 方式命名，例如：com.vendor.root 。在组件根目录下，创建名为 `data` 和 `meta` 的子目录;

    ```shell
    -packages
        - com.vendor.root
            - data
            - meta
        - com.vendor.root.component1
            - data
            - meta
        - com.vendor.root.component1.subcomponent1
            - data
            - meta
        - com.vendor.root.component2
            - data
            - meta
    ```

4. meta 目录包含指定部署和安装过程的文件，这些文件【不会由安装程序提取安装】，meta 目录中必须包含至少一个包信息文件（package.xml ）和你在包信息文件中引用的所有文件，例如：脚本、用户界面文件和翻译等。
5. 将安装程序需要安装的文件（你的exe程序及其依赖等）放到 data 目录中。【包含安装过程中安装程序提取的安装内容】；

### 2.2. 创建配置

1. 在 config 目录中，创建具有以下内容的名为 config.xml 的文件;

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Installer>
        <!-- 程序名称 -->
        <Name>Your application</Name>
        <!-- 版本号 -->
        <Version>1.0.0</Version>
        <Title>Your application Installer</Title>
        <!-- 软件的发布者（公司名称） -->
        <Publisher>Your vendor</Publisher>
        <!-- 在 Windows 开始菜单中为产品指定了默认程序组的名称 -->
        <StartMenuDir>Super App</StartMenuDir>
        <!-- 指定应用程序的默认安装目录 -->
        <TargetDir>@HomeDir@/InstallationDirectory</TargetDir>
        <!-- 如果卸载时不删除目标文件夹,设置为false -->
        <RemoveTargetDir>false</RemoveTargetDir>
    </Installer>
    ```

2. meta 目录中创建一个具有以下内容并名为 package.xml 的文件(组件)：

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Package>
        <DisplayName>The root component</DisplayName>
        <Description>Install this example.</Description>
        <Version>0.1.0-1</Version>
        <ReleaseDate>2010-09-21</ReleaseDate>
        <Licenses>
            <License name="Beer Public License Agreement" file="license.txt" />
        </Licenses>
        <Default>script</Default>
        <Script>installscript.qs</Script>
        <UserInterfaces>
            <UserInterface>page.ui</UserInterface>
        </UserInterfaces>
    </Package>
    ```

### 2.3. 拷贝exe及其依赖

1. 例如：将程序可执行文件及其依赖放到 packages/com.vendor.root/data 目录中.

### 2.4. 创建安装包

1. 切换到 Package 目录，执行下列命令即可：

```shell
binarycreator.exe -c config/config.xml -p packages YourInstaller.exe
```

## 3. 定制安装程序

## 4. 覆盖安装

1. 默认情况下，Qt Installer Framework 不支持离线升级。
2. 如果想要升级，只能先手动卸载旧版程序，再安装新版本的程序。
3. 定制安装程序，来强制覆盖安装新版本程序；或先自动卸载旧版本程序，再安装新版程序。
4. `<Component>/meta/package.xml`

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Package>
        <DisplayName>Atlas4500Tuner</DisplayName>
        <Description>Install the Atlas4500 Tuner</Description>
        <Version>1.0.0</Version>
        <ReleaseDate></ReleaseDate>
        <Default>true</Default>
        <Required>true</Required>
        <!-- 自定义脚本 -->
        <Script>installscript.qs</Script>
        <UserInterfaces>
            <UserInterface>targetwidget.ui</UserInterface>
        </UserInterfaces>
    </Package>
    ```

5. `<Component>/meta/installscript.qs`
    
    `maintenancetool.exe`为Qt的维护软件，生成安装包的时候，会自动加载。

    ```qs
    var targetDirectoryPage = null;

    function Component() 
    {
        installer.gainAdminRights();
        component.loaded.connect(this, this.installerLoaded);
    }

    Component.prototype.createOperations = function() 
    {
        // Add the desktop and start menu shortcuts.
        component.createOperations();
        component.addOperation("CreateShortcut",
                               "@TargetDir@/Atlas4500Tuner.exe",
                               "@DesktopDir@/Atlas4500 Tuner.lnk",
                               "workingDirectory=@TargetDir@");

        component.addOperation("CreateShortcut",
                               "@TargetDir@/Atlas4500Tuner.exe",
                               "@StartMenuDir@/Atlas4500 Tuner.lnk",
                               "workingDirectory=@TargetDir@");
    }

    Component.prototype.installerLoaded = function()
    {
        installer.setDefaultPageVisible(QInstaller.TargetDirectory, false);
        installer.addWizardPage(component, "TargetWidget", QInstaller.TargetDirectory);

        targetDirectoryPage = gui.pageWidgetByObjectName("DynamicTargetWidget");
        targetDirectoryPage.windowTitle = "Choose Installation Directory";
        targetDirectoryPage.description.setText("Please select where the Atlas4500 Tuner will be    installed:");
        targetDirectoryPage.targetDirectory.textChanged.connect(this, this.targetDirectoryChanged);
        targetDirectoryPage.targetDirectory.setText(installer.value("TargetDir"));
        targetDirectoryPage.targetChooser.released.connect(this, this.targetChooserClicked);

        gui.pageById(QInstaller.ComponentSelection).entered.connect(this, this. componentSelectionPageEntered);
    }

    Component.prototype.targetChooserClicked = function()
    {
        var dir = QFileDialog.getExistingDirectory("", targetDirectoryPage.targetDirectory.text);
        targetDirectoryPage.targetDirectory.setText(dir);
    }

    Component.prototype.targetDirectoryChanged = function()
    {
        var dir = targetDirectoryPage.targetDirectory.text;
        if (installer.fileExists(dir) && installer.fileExists(dir + "/maintenancetool.exe")) {
            targetDirectoryPage.warning.setText("<p style=\"color: red\">Existing installation detected and     will be overwritten.</p>");
        }
        else if (installer.fileExists(dir)) {
                targetDirectoryPage.warning.setText("<p style=\"color: red\">Installing in existing     directory. It will be wiped on uninstallation.</p>");
        }
        else {
            targetDirectoryPage.warning.setText("");
        }
        installer.setValue("TargetDir", dir);
    }

    Component.prototype.componentSelectionPageEntered = function()
    {
        var dir = installer.value("TargetDir");
        if (installer.fileExists(dir) && installer.fileExists(dir + "/maintenancetool.exe")) {
            installer.execute(dir + "/maintenancetool.exe", "--script=" + dir + "/scripts/auto_uninstall.qs");
        }
    }
    ```

6. `<Component>/meta/targetwidget.ui`

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ui version="4.0">
     <class>TargetWidget</class>
     <widget class="QWidget" name="TargetWidget">
      <property name="geometry">
       <rect>
        <x>0</x>
        <y>0</y>
        <width>491</width>
        <height>190</height>
       </rect>
      </property>
      <property name="sizePolicy">
       <sizepolicy hsizetype="Preferred" vsizetype="Preferred">
        <horstretch>0</horstretch>
        <verstretch>0</verstretch>
       </sizepolicy>
      </property>
      <property name="minimumSize">
       <size>
        <width>491</width>
        <height>190</height>
       </size>
      </property>
      <property name="windowTitle">
       <string>Form</string>
      </property>
      <layout class="QVBoxLayout" name="verticalLayout">
       <item>
        <widget class="QLabel" name="description">
         <property name="text">
          <string/>
         </property>
        </widget>
       </item>
       <item>
        <layout class="QHBoxLayout" name="horizontalLayout">
         <item>
          <widget class="QLineEdit" name="targetDirectory">
           <property name="readOnly">
            <bool>true</bool>
           </property>
          </widget>
         </item>
         <item>
          <widget class="QToolButton" name="targetChooser">
           <property name="sizePolicy">
            <sizepolicy hsizetype="Fixed" vsizetype="Preferred">
             <horstretch>0</horstretch>
             <verstretch>0</verstretch>
            </sizepolicy>
           </property>
           <property name="minimumSize">
            <size>
             <width>0</width>
             <height>0</height>
            </size>
           </property>
           <property name="text">
            <string>...</string>
           </property>
          </widget>
         </item>
        </layout>
       </item>
       <item>
        <layout class="QHBoxLayout" name="horizontalLayout_2">
         <property name="topMargin">
          <number>0</number>
         </property>
         <item>
          <widget class="QLabel" name="warning">
           <property name="enabled">
            <bool>true</bool>
           </property>
           <property name="text">
            <string>TextLabel</string>
           </property>
          </widget>
         </item>
         <item>
          <spacer name="horizontalSpacer">
           <property name="orientation">
            <enum>Qt::Horizontal</enum>
           </property>
           <property name="sizeHint" stdset="0">
            <size>
             <width>40</width>
             <height>20</height>
            </size>
           </property>
          </spacer>
         </item>
        </layout>
       </item>
       <item>
        <spacer name="verticalSpacer">
         <property name="orientation">
          <enum>Qt::Vertical</enum>
         </property>
         <property name="sizeHint" stdset="0">
          <size>
           <width>20</width>
           <height>122</height>
          </size>
         </property>
        </spacer>
       </item>
      </layout>
     </widget>
     <resources/>
     <connections/>
    </ui>
    ```

7. `<Component>/data/scripts/auto_uninstall.qs`思想是检测当前目录是否有安装，如果有的话，在该目录中运行维护工具（执行卸载方法），并使用一个控制器脚本模拟点击 下一步 按钮

    ```qs
    // Controller script to pass to the uninstaller to get it to run automatically.
    // It's passed to the maintenance tool during installation if there is already an
    // installation present with: <target dir>/maintenancetool.exe --script=<target dir>/scripts/   auto_uninstall.qs.
    // This is required so that the user doesn't have to see/deal with the uninstaller in the middle of
    // an installation.

    function Controller()
    {
        gui.clickButton(buttons.NextButton);
        gui.clickButton(buttons.NextButton);

        installer.uninstallationFinished.connect(this, this.uninstallationFinished);
    }

    Controller.prototype.uninstallationFinished = function()
    {
        gui.clickButton(buttons.NextButton);
    }

    Controller.prototype.FinishedPageCallback = function()
    {
        gui.clickButton(buttons.FinishButton);
    }
    ```

## 创建在线安装程序

1. 创建在线安装应用程序，只需创建一个存储库并将其上传到 web 服务器，然后在 config.xml 文件中指定存储库的位置即可。
