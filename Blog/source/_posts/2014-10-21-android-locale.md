---
title: '[Android] Change locale, default locale, language list'
date: 2014-10-21 22:33
categories: Android
tag: 
	- Locales
	- Language lise
---
在andoird 手機 ODM/OEM公司中. 
常常要配合客戶的SPEC修改預設的語系.還有語言列表. 以及隨著特殊的SPEC修改目前的語系等等
會逐一分享.

## Change Locale 更換語系
先從基本的更換語系開始
```java changelocale
import android.app.ActivityManagerNative;
import android.app.IActivityManager;
import java.util.Locale;
import android.content.res.Configuration;
import android.os.RemoteException;
.........
..........

public void updateLocale() {
    try {
        Locale l = new Locale("en", "US"); //Locale(string_language, string_location)
        IActivityManager am = ActivityManagerNative.getDefault();
        Configuration config = am.getConfiguration();
        config.setLocale(l);
        am.updateConfiguration(config);
    } catch (RemoteException e) {
        Log.d(TAG, "fail to update configuration");
    }
}

```
我記得要更換語系是需要system 權限的，所以別忘記在AndroidManifest.xml要加入 shareUserID=system
```xml AndroidManifest.xml
 <manifest xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.android.settings"
        coreApp="true"
        android:sharedUserId="android.uid.system">

```

## Language List 語言列表
語言列表通常會在哪裡看到呢？
    1. OOBE
    2. Settings->Input method & language

要討論語言列表有兩個角度可以探討:
    - 手機中所有的語言列表有多少？
    - Settings and OOBE 出現能選擇的語言有多少？

**qualcomm /aosp**
可以在 *aosp/build/target/product/fullbase.mk* 發現
```makefile build/target/product/fullbase.mk
$(call inherit-product, $(SRC_TARGET_DIR)/product/locales_full.mk)
```
那麼！*aosp/build/target/product/locales_full.mk* 會看到
```makefile aosp/build/target/product/locales_full.mk
PRODUCT_LOCALES := en_US en_IN fr_FR it_IT es_ES et_EE de_DE nl_NL cs_CZ pl_PL ja_JP zh_TW zh_CN zh_HK ru_RU ko_KR nb_NO es_US da_DK el_GR tr_TR pt_PT pt_BR rm_CH sv_SE bg_BG ca_ES en_GB fi_FI hi_IN hr_HR hu_HU in_ID iw_IL lt_LT lv_LV ro_RO sk_SK sl_SI sr_RS uk_UA vi_VN tl_PH ar_EG fa_IR th_TH sw_TZ ms_MY af_ZA zu_ZA am_ET hi_IN en_XA ar_XB fr_CA km_KH lo_LA ne_NP si_LK mn_MN hy_AM az_AZ ka_GE
..........................
..........................
```
便可以在這邊新增/刪除 客製化語言列表的功能.

**MTK platform**
其實大同小異，只是檔案名稱跟路徑不一樣

```makefile Project/mediatek/config/projectname/ProjectConfig.mk
PRODUCT_LOCALES := en_US en_IN fr_FR it_IT es_ES et_EE de_DE nl_NL cs_CZ pl_PL ja_JP zh_TW zh_CN zh_HK ru_RU ko_KR nb_NO es_US da_DK el_GR tr_TR pt_PT pt_BR rm_CH sv_SE bg_BG ca_ES en_GB fi_FI hi_IN hr_HR hu_HU in_ID iw_IL lt_LT lv_LV ro_RO sk_SK sl_SI sr_RS uk_UA vi_VN tl_PH ar_EG fa_IR th_TH sw_TZ ms_MY af_ZA zu_ZA am_ET hi_IN en_XA ar_XB fr_CA km_KH lo_LA ne_NP si_LK mn_MN hy_AM az_AZ ka_GE
```

總是天不從人願，很多客戶很喜歡同個image有不一樣的設定. 
ex: 隨著SIM卡不同？或是隨著operator變化？

首先，還是得先完成上面的工作。把所有可能會用到的語言添加進mk檔. (看是哪種platform)
再來。請看到 "*frameworks/base/core/java/com/android/internal/app/LocalePicker.java*"

```java LocalePicker.java
public static ArrayAdapter<LocaleInfo> constructAdapter(Context context,
            final int layoutId, final int fieldId, final boolean isInDeveloperMode) {
        final Resources resources = context.getResources();

        ArrayList<String> localeList = new ArrayList<String>(Arrays.asList(
                Resources.getSystem().getAssets().getLocales())); //取得系統目前的所有語言列表
        if (isInDeveloperMode) {
            if (!localeList.contains("zz_ZZ")) {
                localeList.add("zz_ZZ");
            }
        /** - TODO: Enable when zz_ZY Pseudolocale is complete
         *  if (!localeList.contains("zz_ZY")) {
         *      localeList.add("zz_ZY");
         *      }
         */
        }
        String[] locales = new String[localeList.size()];
        locales = localeList.toArray(locales);

        final String[] specialLocaleCodes = resources.getStringArray(R.array.special_locale_codes);
        final String[] specialLocaleNames = resources.getStringArray(R.array.special_locale_names);
        Arrays.sort(locales); //排序語言列表
        ...................................................................
        ...................................................................

```
那其實最關鍵的地方就在於 
```java 
	String[] locales = new String[localeList.size()];
	locales = localeList.toArray(locales); 
```
只要讓locales裝上客製化的語言列表即可.
ex:
```java
String _locale = "en_US,zh_TW";
String locales[] = _locale.split(",");
Arrays.sort(locales);
```

## Default Locale 預設語系
因應會有各客戶或是operator希望出貨時第一次開機的語系能跟出貨地區配合
所以有研究這個機制 (其實也沒什麼好研究的 囧)

首先先看會決定語系的幾個property

```
persist.sys.language=zh
persist.sys.region=TW
ro.product.locale.language=en
ro.product.locale.country=US
```
Locale = language_country(or region)
Android 系統會優先以 persist 開頭的property作為目前系統的語系
如上面的例子：目前系統的顯示語言應為 繁體中文 (zh_TW)

1. 所以可以在 project/build/core/Makefile 中修改, 加入persist或是在init.rc跟system.prop 加上都可以	
**Ex: Qualcomm platform**
```makefile device/qcom/msmXXXX/system.prop
persist.sys.language=zh
persist.sys.region=TW
```
	
2. Android系統有個機制，在persist.sys.laguage跟persist.sys.region沒有value時：**系統會把SIM的語系資料套用到整個系統**.
Ex: 再第一次開機或是Factory reset後. 手機已經插入Orange的SIM卡，開機的預設語言變化設成 **fr_FR**. 	
所以按照第一點的方式去預設語系，則此功能將不會有作用 = issue. (其實是看客戶啦.)	
那麼，就改設ro.product.locale.language跟ro.product.locale.country.	
範例其實跟第一點一樣，只是改成ro開頭的prop
**Ex: Qualcomm platform**
```makefile device/qcom/msmXXXX/system.prop
ro.product.locale.language=zh
ro.product.locale.country=TW
```
如果是有更複雜的需求，如開機後才判斷預設語系要設成那一組
上面的方法可能比較不彈性。
我個人的經驗，曾經在幾個地方設置過機制
首先. 把下面這段code拿掉 or 註解， 如此就不會有ro跟persist開頭的語系properties生成
```sh build/tools/buildinfo.sh
..............................
..............................
#echo "ro.product.manufacturer=$PRODUCT_MANUFACTURER"
if [ -n "$PRODUCT_DEFAULT_LANGUAGE" ] ; then
  echo "ro.product.locale.language=$PRODUCT_DEFAULT_LANGUAGE"
fi
if [ -n "$PRODUCT_DEFAULT_REGION" ] ; then
  echo "ro.product.locale.region=$PRODUCT_DEFAULT_REGION"
fi
.....................................
.....................................
```
	- 在 依照需求建立不同的.prop檔. 然後在system/core/init/property_service.c中load.
  - 或是在 system/core/init/init.c 中設定
  - 最後就是在 frameworks/base/core/jni/AndroidRuntime.cpp 中設定
  
```java AndroidRuntime.cpp
static void readLocale(char* language, char* region) {
    char propLang[PROPERTY_VALUE_MAX], propRegn[PROPERTY_VALUE_MAX];
    property_get("persist.sys.language", propLang, "");
    property_get("persist.sys.country", propRegn, "");
    if (*propLang == 0 && *propRegn == 0) {//在persist.sys.language跟persist.sys.country為空時進入
        /* Set to ro properties, default is en_US */
        /* 可以將下列的 en 與 US 替換成所需要的locale */
        property_get("ro.product.locale.language", propLang, "en");
        property_get("ro.product.locale.region", propRegn, "US");
    }
    strncat(language, propLang, 2);
    strncat(region, propRegn, 2);
    //ALOGD("language=%s region=%s\n", language, region);
}
```



--------------------------------------------------------------------------------------------

Update at 2016: Android M

遇到issue了,  回過頭看一下發現在Android M的機制似乎有更改：

property:
新增了兩組：`ro.product.locale` 與 `persist.sys.locale`
  
```sh build/tools/buildinfo.sh
..............................
..............................
#echo "ro.product.manufacturer=$PRODUCT_MANUFACTURER"
if [ -n "$PRODUCT_DEFAULT_LANGUAGE" ] ; then
  echo "ro.product.locale.language=$PRODUCT_DEFAULT_LANGUAGE"
fi
if [ -n "$PRODUCT_DEFAULT_REGION" ] ; then
  echo "ro.product.locale.region=$PRODUCT_DEFAULT_REGION"
fi
.....................................
.....................................
```
上面這組sh 被替換成：
```sh build/tools/buildinfo.sh
..............................
..............................
#echo "ro.product.manufacturer=$PRODUCT_MANUFACTURER"
if [ -n "$PRODUCT_DEFAULT_LOCALE" ] ; then
  echo "ro.product.locale=$PRODUCT_DEFAULT_LOCALES"
fi
.....................................
.....................................
```
ro.product.locale.language與ro.product.locale.region 已經在此檔案中移除

在AndroidRuntime.cpp 在readlocale() 中的機制也有所更改
```cpp AndroidRuntime.cpp
const std::string readLocale()
{
    const std::string locale = getProperty("persist.sys.locale", "");
    if (!locale.empty()) {
        return locale;
    }

    const std::string language = getProperty("persist.sys.language", "");
    if (!language.empty()) {
        const std::string country = getProperty("persist.sys.country", "");
        const std::string variant = getProperty("persist.sys.localevar", "");

        std::string out = language;
        if (!country.empty()) {
            out = out + "-" + country;
        }

        if (!variant.empty()) {
            out = out + "-" + variant;
        }

        return out;
    }

    const std::string productLocale = getProperty("ro.product.locale", "");
    if (!productLocale.empty()) {
        return productLocale;
    }

    // If persist.sys.locale and ro.product.locale are missing,
    // construct a locale value from the individual locale components.
    const std::string productLanguage = getProperty("ro.product.locale.language", "en");
    const std::string productRegion = getProperty("ro.product.locale.region", "US");

    return productLanguage + "-" + productRegion;
}
```
    - 先確認 persist.sys.locale這個property是否存在？ return persist.sys.locale : next step
    - 確認 persist.sys.locale.language/region存在？ return persist.sys.locale.language + region : next step
    - 確認ro.product.locale是否存在？ return ro.product.locale : next step
    - 確認ro.product.locale.language/region 存在？ return ro.product.locale.language + region : return en_US

以上是Android M的更新

