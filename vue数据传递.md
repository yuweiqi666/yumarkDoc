# vue数据传递

### 父子组件传值

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