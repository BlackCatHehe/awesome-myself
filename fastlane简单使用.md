**Fastlane normal use**

### 1  Install

```ruby
brew install fastlane
```



### 2  Init to project

```ruby
cd [your project root folder]
fastlane init
bundle update
```



### 3  Add usefully plugins

* fastlane-plugin-icon_versioning(generate mask text for your appicon)

```swift
//basic 
brew install ImageMagick
//install plugin
bundle exec fastlane add_plugin fastlane-plugin-icon_versioning
//use as fastlane
lane :icon_mask do |options|
version_icon(
appiconset_path: "./projectName/Assets.xcassets/AppIcon.appiconset",
text: "#{options[:t]} #{options[:v]}(#{options[:n]})"
)
end
//then your can find a named "A_AppIcon-Versioned" file in your project
```

* fir-cli

```swift
//install plugin
bundle exec fastlane add_plugin fir-cli
//use as fastlane
lane :fir do
       fir_cli api_token: "fir-token", 
               specify_file_path: "path/to/appName.ipa", //需要上传的app.ipa文件位置
           specify_icon_file: "./projectName/Assets.xcassets/A_AppIcon-Versioned.appiconset", //需要上传的appicon文件位置（这里我使用的是上面的插件生成的appicon，如果不需要，则直接删除改参数）
           changelog: "upload to fir success!"
end
      ```

### 4  Archive

```swift
//测试环境，可根据需求修改环境
lane :dev do |options|
				//generate new appicon if needed, if not nedd, delete
        icon_mask(t: "test", v: options[:v], n: options[:n])
//gym build your project
	gym(
            workspace: "Weplate.xcworkspace",
            scheme: "Weplate",
            clean: true,
            output_directory: "./output",
            output_name: "MVMaker",
            configuration: "Debug",
            silent: true,
            export_method: "development",
 	    export_options: {
               method: "development", //导出模式development, ad-hoc, app-store
               provisioningProfiles: { 
                  "com.ideafrom.video.editor.mvmaker.test" => "mv-maker_dev"
                 //bundleid => profileName
               }
            },
	    export_xcargs: "-allowProvisioningUpdates",
    				//打包需要的appicon,如果不需要更换则删除
            xcargs: "ASSETCATALOG_COMPILER_APPICON_NAME=A_AppIcon-Versioned",
	    suppress_xcode_output: true
        )
	//使用上面的fir-cli插件上传到fir
        fir
  end
```



### 5. urls

* gym:  https://docs.fastlane.tools/actions/gym/
* fir-cli: https://github.com/FIRHQ/fastlane-plugin-fir_cli
