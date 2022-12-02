---
title: Vue3 筆記
categories: Node.js
date: 2022-12-02 17:10:02
tags: Node.js Vue
---

以前寫的 Vue2 都忘得差不多了，藉著最近接到 Vue3 專案的機會，重新學習一下

Vue3 推薦結合以下套件使用，透過官方推薦的初始化指令 `npm init vue@latest` 可自動建立一個基礎的 Vue 專案

* Vite: 建置工具
* Pinia: 狀態管理

## Import

CDN：

``` js
<script src="https://unpkg.com/vue@next"></script>
```

<!-- more -->

## Virtual DOM

Vue 並非直接對頁面進行渲染，其會先於背景的 Virtual DOM 運算，當確定整個頁面結構後，再一次性地將結果繪製到頁面中，以此提升效能

![](https://i.imgur.com/BFCmFYv.png)

## reactive(): 資料連動

基本單向的資料連動

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://unpkg.com/vue@next"></script>
</head>
<body>
    <div id="app">
        <p>Type: {{ framework.type }}</p>
        <p>Framework: {{ framework.name }}</p>
    </div>
</body>
</html>
<script>
    const { reactive } = Vue;
    const app = {
        setup(){
            const framework = reactive({
                type: "Frontend",
                name: "Vue",
            })
            return { framework }
        }
    }
    const myVue = Vue.createApp(app).mount("#app");
</script>
```

也可以先新增好物件再掛載

``` js
const framework = {
    data() {
        return {
            type: 'Frontend',
            name: 'Vue',
        }
    }
}
const app = Vue.createApp(framework).mount('#app');
```

### v-model

在v-model中能加入一些修飾符來加強運用，可以使用的修飾符如下：

v-model.lazy: 在文字方塊onChange時才會觸發更新。
v-model.number: 將輸入值轉換為數值格式。
v-model.trim: 去除欄位中的前後空白字元。

``` html
<div id="app">
    <p>
        <select v-model="framework.type">
            <option value="Frontend">Frontend</option>
            <option value="Backend">Backend</option>
        </select>
    </p>
    <p>
        <select v-model="framework.name">
            <option value="Vue">Vue</option>
            <option value="React">React</option>
            <option value="Angular">Angular</option>
            <option value="Laravel">Laravel</option>
            <option value="CakePHP">CakePHP</option>
            <option value="Django">Django</option>
        </select>
    </p>
    <p>Type: {{ framework.type }}</p>
    <p>Framework: {{ framework.name }}</p>
</div>

<script>
const { reactive } = Vue;
const app = {
    setup(){
        const framework = reactive({
            type: "Frontend",
            name: "Vue",
        })
        return {framework};
    }
}
const myVue = Vue.createApp(app).mount("#app");
</script>
```

### v-for

``` html
<div id="app">
    <ul>
        <li v-for="(value, name, index) in state.frameworks">
            {{ index + 1 }}. {{ name }} - {{ value }}
        </li>
    </ul>
</div>

<script>
const { reactive } = Vue;
const app = {
    setup() {
        const state = reactive({
            frameworks: {
                Vue: "Frontend",
                React: "Frontend",
                Angular: "Frontend",
                Laravel: "Backend",
                CakePHP: "Backend",
                Django: "Backend",
            },
        })
        return { state };
    }
}
const myVue = Vue.createApp(app).mount("#app");
</script>
```

### v-bind

v-bind是在HTML屬性中，如果需要用到vue data時使用

* :value=修飾符寫法
* :key=綁定標題/內容，要注意給的key值是不能有重複的，否則頁面會發生錯誤

``` html
<div id="app">
    <p>
        <select v-model="state.type">
            <option v-for="item in state.types" v-bind:value="item.value">{{ item.title }}</option>
        </select>
    </p>
    <p>
        <select v-model="state.name">
            <option v-for="item in state.names" :key="item.title" :value="item.value">{{ item.title }}</option>
        </select>
    </p>
    <p>Type: {{ state.type }}</p>
    <p>Framework: {{ state.name }}</p>
</div>

<script>
const { reactive } = Vue;
const app = {
    setup() {
        const state = reactive({
            type: "Frontend",
            name: "Vue",
            names:[
                {title: "Vue", value: "Vue"},
                {title: "React", value: "React"},
                {title: "Angular", value: "Angular"},
                {title: "Laravel", value: "Laravel"},
                {title: "CakePHP", value: "CakePHP"},
                {title: "Django", value: "Django"},
            ],
            types:[
                {title: "Frontend", value: "Frontend"},
                {title: "Backend", value: "Backend"},
            ],
        })
        return { state };
    }
}
const myVue = Vue.createApp(app).mount("#app");
</script>
```

## Event

``` html
<div id="app">
    <p>
        <select v-on:change="changeValue($event, 'type')">
            <option value="Frontend">Frontend</option>
            <option value="Backend">Backend</option>
        </select>
    </p>
    <p>
        <select v-on:change="changeValue($event, 'name')">
            <option value="Vue">Vue</option>
            <option value="React">React</option>
            <option value="Angular">Angular</option>
        </select>
    </p>
    <p>Type: {{ framework.type }}</p>
    <p>Framework: {{ framework.name }}</p>
</div>

<script>
    const { reactive } = Vue;
    const app = {
        setup(){
            const framework = reactive({
                type: "Frontend",
                name: "Vue",
            })
            function changeValue(evt, id) {
                framework[id] = evt.currentTarget.value;
            }
            return {framework, changeValue};
        }
    }
    const myVue = Vue.createApp(app).mount("#app");
</script>
```

v-on 可以用 @ 縮寫如下：

``` html
<select @change="changeValue($event, 'type')">
```

### 事件修飾符

* @click.prevent: preventDefault
* @click.stop: 阻止事件向上傳遞
* @click.capture: 先將本身觸發的函數先執行完畢後再下傳事件的效果。 Ex. 使用此語法後執行順序 = navFn > submitFn
* @click.self: 只在被點擊對象是自己的時候執行
* @click.once: 僅第一次按下時觸發動作

### 按鍵修飾符

* @keydown.prevent：會擋住在表單範圍內按下Enter鍵之後，自動送出表單的功能
* @keydown.ctrl.enter=”enterFn”：使用者在此input中按下Ctrl + Enter時會觸發enterFn
* @click.prevent.alt.exact=”clickFn”：使用者在需要按下鍵盤上的Alt + 滑鼠左鍵點擊按鈕才會觸發ClickFn，若將其中exact的部分拿掉，則按下鍵盤Alt + Shift + 左鍵，也會觸發該函數。

``` html
<div id="app">
    <div class="header">
        <div class="nav">
            <form @keydown.prevent>
                <input type="text" @keydown.ctrl.enter="enterFn">
                <button @click.prevent.alt.exact="clickFn">檢查</button>

                <input type="submit" @click.prevent.stop="submitFn" value="Submit">
                <input type="submit" @click.prevent.once="submitFn" value="Submit Once">
                <input type="button" @click.stop="clearFn" value="Clear">

            </form>
        </div>
    </div>
    <div v-html="state.action"></div>
</div>

<script>
    const { reactive } = Vue;
    const app = {
        setup() {
            const state = reactive({
                action: "",
            });
            function submitFn() {
                state.action += "表單送出<br>";
            }
            function navFn() {
                state.action += "選單點選<br>";
            }
            function clearFn() {
                state.action = "";
            }
            function enterFn() {
                state.action += "enterFn()<br>";
            }
            function clickFn() {
                state.action += "clickFn()<br>";
            }
            return { state, submitFn, navFn, clearFn, enterFn, clickFn };
        }
    }
    const myVue = Vue.createApp(app).mount("#app");
</script>
```

## computed(): 計算屬性

Function 寫法：

``` html
<div id="app">
    <p>
        <input type="text" v-model="state.number1"> + 
        <input type="text" v-model="state.number2"> = {{ result() }}
    </p>
</div>

<script>
const { reactive } = Vue;
const app = {
    setup(){
        const state = reactive({
            number1: 0,
            number2: 0,
        })
        function result(){
            if(isNaN(state.number1) || isNaN(state.number2)){
                return "Error!! Please enter the number."
            }else{
                return parseFloat(state.number1) + parseFloat(state.number2);
            }
        }
        return { state, result };
    }
}
const myVue = Vue.createApp(app).mount("#app");
</script>
```

Computed 寫法：僅針對參考資料異動時才會觸發運算 > 效能較佳

``` html
<div id="app">
    <p>
        <input type="number" v-model="state.number1"> +
        <input type="number" v-model="state.number2"> = {{ result }}
    </p>
</div>

<script>
const { reactive, computed } = Vue;
const app = {
    setup(){
        const state = reactive({
            number1: 0,
            number2: 0,
        })
        const result = computed(() => {
            if(isNaN(state.number1) || isNaN(state.number2)){
                return "Error!! Please enter the number."
            }else{
                return parseFloat(state.number1) + parseFloat(state.number2);
            }
        })
        return { state, result };
    }
}
const myVue = Vue.createApp(app).mount("#app");
</script>
```

### Setter

預設 Computed 為 read-only，若直接修改結果 Console 會跳出警告：`Write operation failed: computed value is readonly`

解法：

``` html
<div id="app">
    <p>Height:<input type="number" v-model="state.height">CM</p>
    <p>Weight:<input type="number" v-model="state.weight">KG</p>
    <p>BMI:<input type="number" v-model="bmi"></p>
    <p>Suggestion:{{ bmiMessage }}</p>
</div>

<script>
const { reactive, computed } = Vue;
const app = {
    setup(){
        const state = reactive({
            height: 180,
            weight: 80,
        })
        const bmi = computed({
            get:() => {
                return (state.weight / (Math.pow((state.height / 100), 2))).toFixed(2);
            },
            set:(val) => {
                console.log("Run Setter");
                state.weight = ((Math.pow((state.height / 100), 2)) * val).toFixed(2);
            }
        })
        const bmiMessage = computed(() => {
            console.log("Run BMI Message Computed");
            if (bmi.value > 35) {
                return "重度肥胖";
            } else if (bmi.value >= 30 && bmi.value < 35) {
                return "中度肥胖";
            } else if (bmi.value >= 27 && bmi.value < 30) {
                return "輕度肥胖";
            } else if (bmi.value >= 24 && bmi.value < 27) {
                return "過重";
            } else if (bmi.value >= 18.5 && bmi.value < 24) {
                return "正常範圍";
            } else {
                return "體重過輕";
            }
        })
        return { state, bmi, bmiMessage };
    }
}
const myVue = Vue.createApp(app).mount("#app");
</script>
```

## Watch(): 監聽器

Watch主要是去監聽在Vue裡面的數據，若數據發生變化的時候，會自動去執行相對應的動作

Computed 範例改寫：

``` html
<div id="app">
    <p>Height:<input type="number" v-model="state.height">CM</p>
    <p>Weight:<input type="number" v-model="state.weight">KG</p>
    <p>BMI: {{ state.bmi }}</p>
    <p>Suggestion:{{ state.bmiMessage }}</p>
</div>

<script>
const { reactive, watch } = Vue;
const app = {
    setup(){
        const state = reactive({
            height: 180,
            weight: 80,
            bmi: 24.69,
            bmiMessage: "過重",
        })
        watch(() => [state.height, state.weight], () =>{
            generateBMI();
        })
        function generateBMI(){
            state.bmi = (state.weight / (Math.pow((state.height / 100), 2))).toFixed(2);
            if (state.bmi > 35) {
                state.bmiMessage = "重度肥胖";
            } else if (state.bmi >= 30 && state.bmi < 35) {
                state.bmiMessage = "中度肥胖";
            } else if (state.bmi >= 27 && state.bmi < 30) {
                state.bmiMessage = "輕度肥胖";
            } else if (state.bmi >= 24 && state.bmi < 27) {
                state.bmiMessage = "過重";
            } else if (state.bmi >= 18.5 && state.bmi < 24) {
                state.bmiMessage = "正常範圍";
            } else {
                state.bmiMessage = "體重過輕";
            }
        }
        return { state };
    }
}
const myVue = Vue.createApp(app).mount("#app");
</script>

```

使用情境：

``` html
<div id="app">
    <p>用戶姓名：
        <input type="text" v-model="state.username" placeholder="請輸入5~15個小寫英文，符號僅能使用@-_" size="50">
        <span class="errorMessage">{{ state.usernameMsg }}</span>
    </p>
</div>

<script>
const { reactive, watch } = Vue;
const app = {
    setup(){
        const state = reactive({
            username: "",
            usernameMsg: "",
        })
        watch(() => state.username, (value) =>{
            state.username = state.username.replace(/[^a-zA-Z0-9_@-]/g, "");
            const usernameReg = /^.{5,15}$/;
            if (usernameReg.test(value)) {
                state.usernameMsg = "";
            } else {
                state.usernameMsg = "請輸入5~15個字以上";
            }
        })
        return { state };
    }
}
const myVue = Vue.createApp(app).mount("#app");
</script>
```

Replace那一行一定會有問題，因為他是要將值回寫到username中，而Computed還需要透過Setter來做那相對來說就麻煩很多，那Function呢？當然以上都是可行的作法，但都不如Watch來得方便，所以沒有什麼是一定要用到誰，而是透過不同的使用情境來決定！

## 多層下拉選單

``` html
<div id="app">
    <h3>Please make your decision:</h3>
    <p>
        Gender:
        <select v-model="state.genderIdx">
            <option v-for="(item, index) in state.clothes" :value="index">
                {{item.gender}}
            </option>
        </select>
    </p>
    <p>
        Type:
        <select v-model="state.partIdx">
            <option v-for="(item, index) in pickTypes" :value="index">
                {{item.part}}
            </option>
        </select>
    </p>
    <p>
        Product:
        <select v-model="state.itemIdx">
            <option v-for="(item, index) in pickParts" :value="index">
                {{item.product}}
            </option>
        </select>
    </p>
</div>

<script>
const { reactive, computed, watch } = Vue;
const app = {
    setup(){
        const state = reactive({
            genderIdx: 0, // 記錄第一層選單的被選取項目
            partIdx: 0, // 記錄第二層選單的被選取項目
            itemIdx: 0, // 記錄第三層選單的被選取項目
            clothes : [
            {
                gender: "男",
                types: [
                    {
                        part: "上衣類",
                        contents: [
                            { product: "短袖/背心" },
                            { product: "長袖" },
                            { product: "立領/高領" },
                            { product: "針織衫" },
                            { product: "休閒襯衫" },
                            { product: "商務襯衫" },
                            { product: "法蘭絨系列" },
                            { product: "厚棉系列" },
                        ],
                    },
                    {
                        part: "外套類",
                        contents: [
                            { product: "休閒外套" },
                            { product: "Fleece系列" },
                            { product: "極輕羽絨" },
                            { product: "極暖羽絨" },
                        ],
                    },
                    {
                        part: "下身類",
                        contents: [
                            { product: "短/七分褲" },
                            { product: "九分/束口褲" },
                            { product: "休閒長褲" },
                            { product: "牛仔褲" },
                            { product: "保暖褲" },
                        ],
                    },
                    {
                        part: "家居服",
                        contents: [
                            { product: "家居套裝" },
                            { product: "家居褲" },
                            { product: "家居毯" },
                        ],
                    },
                ],
            },
            {
                gender: "女",
                types: [
                    {
                        part: "上衣類",
                        contents: [
                            { product: "印花短T" },
                            { product: "印花長T" },
                            { product: "短袖/背心" },
                            { product: "七分/長袖" },
                            { product: "長版上衣" },
                            { product: "針織衫" },
                            { product: "polo衫" },
                            { product: "Pima棉" },
                        ],
                    },
                    {
                        part: "外套類",
                        contents: [
                            { product: "休閒外套" },
                            { product: "Fleece系列" },
                            { product: "極輕羽絨" },
                            { product: "極暖羽絨" },
                        ],
                    },
                    {
                        part: "下身類",
                        contents: [
                            { product: "休閒短褲" },
                            { product: "七/九分褲" },
                            { product: "牛仔系列" },
                            { product: "寬褲系列" },
                            { product: "休閒長褲" },
                            { product: "裙子" },
                            { product: "連身/吊帶褲" },
                            { product: "緊身褲" },
                            { product: "裙子" },
                        ],
                    },
                    {
                        part: "洋裝",
                        contents: [
                            { product: "洋裝" },
                            { product: "家居褲" },
                            { product: "吊帶裙" },
                        ],
                    },
                ],
            },
            ],
        })
        const pickTypes = computed(() => {
            return state.clothes[state.genderIdx].types;
        })
        const pickParts = computed(() => {
            return state.clothes[state.genderIdx].types[state.partIdx].contents;
        })
        watch(() => state.genderIdx, (value) =>{
            state.partIdx = 0;
        })
        watch(() => state.genderIdx, (value) =>{
            state.itemIdx = 0;
        })
        return { state, pickTypes, pickParts };
    }
}
const myVue = Vue.createApp(app).mount("#app");
</script>
```

## 生命週期

![生命週期](https://asus.cloudlab.tw/ue_atd/wp-content/uploads/2020/08/lifecycle-1.png)
