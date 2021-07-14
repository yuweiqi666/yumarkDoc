# vue数据传递

## 父子组件传值

* 在子组件中使用`$emit`自定义事件（同时可以传值）, 在父组件中触发事件来对数据进行操作

  > 1. 父组件`family.vue`：家庭资产
  > 2. 子组件`father.vue`：父亲资产     父亲具有赚钱属性  每次赚钱资产 + 5000
  > 3. 资产数据为父组件通过`props`传到子组件 所以**家庭资产 = 父亲资产** 

  ![父子组件1](.\imgs\父子组件1.png)

  > 父组件`family.vue`中
  >
  > 1. 父组件中监听子组件的自定义事件`earnMoney`
  > 2. 子组件中自定义事件就触发 通过传递的数据对资产进行更改

  ![父组件中](.\imgs\父组件中.png)

  > 在子组件`father.vue`中
  >
  > 1. 在子组件中点击按钮触发点击事件 自定义`earnMonrey`事件 传值（每次赚多少钱）

  ![子组件中](.\imgs\子组件中.png)



* 通过`.async`修饰符

  > .async修饰符只是一种语法糖， 一种简写形式，本质还是子组件中$emit触发父组件中的事件来操作数据

  * 不使用.async修饰符

    > 父组件`family.vue`中

    ![async父组件](.\imgs\async父组件.png)

    ![async父组件2](.\imgs\async父组件2.png)

    > 在子组件`father.vue`中

    ![async子组件](.\imgs\async子组件.png)

  * 使用.async修饰符（**注意点：**emit自定义事件中传的参数必须和.async前面绑定的数据一样）

    > 父组件`family.vue`中

    ![async修饰符3](.\imgs\async修饰符3.png)

    * 更加简化的写法

      > 将子组件传给父组件的参数名称和父组件传给子组件的参数名称设置为一样

      ![async修饰符3](.\imgs\.async修饰符3.png)

      ![sync修饰符4](.\imgs\.sync修饰符4.png)

* 通过`v-model`

  > .async修饰符也只是一种语法糖， 一种简写形式，本质也还是子组件中$emit触发父组件中的事件来操作数据

  * 不使用`v-model`语法糖

    > 父组件`family.vue`中

    ![v-model1](.\imgs\v-model1.png)

    > 子组件`father.vue`中

    ![v-model2](.\imgs\v-model2.png)

    ​	

  * 使用`v-model`语法糖

    ![v-model3](.\imgs\v-model3.png)





##  多级嵌套组件传值

> 以三层嵌套组件为例

![多层组件传值](E:\桌面\多层组件传值.png)

### 自里到外$emit传递自定义事件

> 相当于子组件自定义时间  父组件触发这个事件

* 传统的通过`$emit`逐层向外触发自定义事件

  > 1. 对于多级嵌套的组件需要从内到外的每一层都$emit自定义事件

  * 在`child`组件中 自定义`getMoney`事件

    ![child组件](.\imgs\child组件.png)

  * 在`child`的父组件`father`组件中触发getMoney事件 同时father组件也自定义`getMoney`事件

    ![father](.\imgs\father.png)

  * 在`father`组件的父组件`grandPa`组件中触发`father`组件的自定义`getMoney`事件对数据进行处理

    ![grandPa](.\imgs\grandPa.png)

* 使用`$parent` 在孙子组件中直接给其父组件自定义事件

  > 1. 对于多层组件嵌套可以极大的减少$emit自定义事件的次数
  > 2. 只需要在最里面的一层组件中通过$parent找到倒数第二层的组件对其进行自定义事件操作
  > 3. 如果大于三层的组件嵌套  在最里面一层需要通过this.$parent.$parent.$paren...找到倒数第二层的组件，没有从本质上解决问题

  * 在`child`孙子组件中

    ![$parent](.\imgs\$parent.png)

* 自己封装一个可以在多层嵌套的组件中进行$emit事件传递的方法挂载在Vue的原型上

  * 第一种封装

    > **缺点：**只适用于**》=3**层的组件自定义事件的穿透

    ![$eventDispatch](.\imgs\$eventDispatch.png)

    > 在嵌套组件的最里层使用这个方法

    ![最里层组件](.\imgs\最里层组件.png)

  * 第二种封装

    > 可以适用于任何层数组件嵌套的自定义事件穿透

    ![嵌套组件事件封装2](.\imgs\嵌套组件事件封装2.png)



### 自外到里传递事件（通知）

> 1. 最外层的组件做了一个操作， 通知到这个组件中所有的子组件 （在子组件中监听父组件的通知事件）
> 2. 与$emit相对应 相当于父组件自定义一个事件  所有的子组件都可以触发这个事件

![notice](.\imgs\notice.png)

> 在最外层的`grandPa`组件中自定义事件

![grandPanotice](.\imgs\grandPanotice.png)

> 在`grandPa`的子组件监听事件

![notice子组件](.\imgs\notice子组件.png)

![notice子组件2](.\imgs\notice子组件2.png)

![notice子组件3](.\imgs\notice子组件3.png)