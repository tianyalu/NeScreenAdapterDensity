### NeScreenAdapterDensity 修改系统density,densityDpi适配(推荐使用)
根据参考宽度和手机真实宽度得到密度缩放比，修改系统density，densityDpi, 使不同密度的手机有相同的密度展示效果  
`注意：XML文件中的宽高需要用dp` 
#### 1. 示例代码如下所示：
Density:  
```android
    /**
     * 以此为参考，比如要设置屏幕宽度一半，只需要设置 WIDTH/2 dp 即可
     */
    private static final float WIDTH = 330; //参考设备的宽，单位是dp  360 / 2 = 180

    private static float appDensity; //表示屏幕密度
    private static float appScaleDensity; //字体缩放比例，默认appDensity

    public static void setDensity(final Application application, Activity activity) {
        //获取当前app的屏幕显示信息
        DisplayMetrics displayMetrics = application.getResources().getDisplayMetrics();
        if(appDensity == 0) {
            //初始化赋值操作
            appDensity = displayMetrics.density;
            appScaleDensity = displayMetrics.scaledDensity;

            //添加字体变化监听回调
            application.registerComponentCallbacks(new ComponentCallbacks() {
                @Override
                public void onConfigurationChanged(Configuration newConfig) {
                    //字体发生更改，重新对scaleDensity进行赋值
                    if(newConfig != null && newConfig.fontScale > 0) {
                        appScaleDensity = application.getResources().getDisplayMetrics().scaledDensity;
                    }
                }

                @Override
                public void onLowMemory() {

                }
            });
        }

        //计算目标density，scaleDensity, densityDpi
        float targetDensity = displayMetrics.widthPixels / WIDTH;  // 1080 / 360 = 3.0
        float targetScaleDensity = targetDensity * (appScaleDensity / appDensity);
        int targetDensityDpi = (int) (targetDensity * 160);

        //替换Activity的density，scaleDensity, densityDpi
        DisplayMetrics dm = activity.getResources().getDisplayMetrics();
        dm.density = targetDensity;
        dm.scaledDensity = targetScaleDensity;
        dm.densityDpi = targetDensityDpi;
    }
```
`setDensity()`方法可以放在`BaseActivity`的`onCreate()`方法中调用，也可以放在`App`中`ActivityLifecycleCallbacks()`的回调方法中调用  

#### 2. 展示效果如下图所示：
![image](https://github.com/tianyalu/NeScreenAdapterDensity/blob/master/show/show.png)
