Glide 梳理

**加载流程**

with(FragmentActivity)  ---> 外观模式，工厂模式；

在 with() --> Glide.get(Context) 中有个技巧，getSupportRequestManagerFragment 方法中，因为使用了 handler 来创建对象。所以这里利用 map.get、map.put 方法来防止重复创建对象；