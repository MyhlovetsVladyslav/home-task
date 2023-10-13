<template>
    <div>
        <p>Перше завдання/Друге завдання</p>
        <p>My name is : {{ items[0].name }}</p>
        <p>Age : {{ items[0].age }}</p>
        <button @click="changeAge">Кнопка</button>
        <hr>
        <p>Третє завдання</p>
        <input type="text" @input="changeName">
        <hr>
        <p>Четверте завдання</p>
        <li v-for="item in items">{{ item.name }} - {{ item.age }} років</li>
        <button @click="sortedItems">Фильтрация по возростанию</button>
        <hr>
        <p>П'яте завдання</p>
        <my-second-component :message="parentMessage" @childEvent="ChildEvent"></my-second-component>
        <hr>
        <p>Шосте завдання</p>
        <form @submit.prevent="submitForm">
            <label for="name">Ім'я:</label>
            <input type="text" id="name" v-model="newName" required><br>
            <label for="age">Вік:</label>
            <input type="number" id="age" v-model="newAge" required><br>
            <button @click="addItem">Підтвердити</button>
        </form>
        <hr>
        <p>Сьоме завдання</p>
        <div>Сумма років: {{ sum }}</div>
        <hr>
        <p>Восьме завдання</p>
        <div>Фільтр</div>
        <ul>
            <li v-for="item in filteredItems" :key="item.id">{{ item.name }} - {{ item.age }} років</li>
        </ul>
        <hr>
        <p>Дев'яте завдання</p>
        <div>{{ combinedData }}</div>
        <hr>
        <p>Десяте завдання</p>
        <div>x: {{ firstComputed }}</div>
        <hr>
        <p>Одинадцяте завдання</p>
        <p>Значення змінної user.age: {{ user.age }}</p>
        <button @click="changeDataUser">Змінити значення</button>
        <hr>
        <p>Дванадцяте завдання</p>
        <p>Значення змінної items[0].age: {{ items[0].age }}</p>
        <button @click="changeDataItem">Змінити значення</button>
        <hr>
        <p>Тринадцяте завдання</p>
        <p>Значення змінної x: {{ x }}</p>
        <button @click="changeDataX">Змінити значення</button>
        <hr>
        <p>Чотирнадцяте завдання</p>
        <p>{{ doubleCounter }}</p>
        <button @click="addToCounter">Кнопка</button>
        <hr>
        <p>П'ятнадцяте завдання/Шістнадцяте завдання</p>
        <li v-for="item in items">{{ item.name }} - {{ item.age }} років</li>
    </div>
</template>
  
<script>
import SecondComponent from './SecondComponent.vue';
export default {
    components: {
        'my-second-component': SecondComponent
    },
    data() {
        return {
            counter: 0,
            x: 0,
            user: {
                name: 'Vlad',
                age: 25
            },
            userDataMessage: "Hello,",
            userData: null,
            secondUserData: null,
            parentMessage: 'Як справи?',
            newName: '',
            newAge: null,
            items: [
                { name: 'Vlad', age: 25 },
                { name: 'Sanya', age: 30 },
                { name: 'Zhan', age: 28 },
                { name: 'Vika', age: 22 },],
            oldItems: null
        };
    },
    methods: {
        sortedItems() {
            return this.items.sort((a, b) => {
                return (a.age - b.age);
            });
        },
        changeAge() {
            this.items[0].age = 26;
        },
        changeName(event) {
            this.items[0].age = event.target.value
        },
        ChildEvent(message) {
            this.parentMessage = message;
        },
        changeDataUser() {
            this.user.age = 29;
        },
        changeDataItem() {
            this.items[0].age = 29;
        },
        changeDataX() {
            this.x = 12;
        },
        async sendApiRequest() {
            await fetch('https://jsonplaceholder.typicode.com/users/2')
                .then(response => response.json())
                .then(data => {
                    this.secondUserData = data.name;
                })
                .catch(error => {
                    console.error('Помилка:', error);
                });
            console.log('Відправлено API запит з даними:', this.secondUserData);
        },
        async sendApi() {
            try {
                let response = await fetch('https://jsonplaceholder.typicode.com/users/1');
                let data = await response.json();
                this.userData = data.name;
            } catch (error) {
                console.error('Помилка:', error);
                throw error;
            }
        },
        addItem() {
            if (this.newName.length > 3 && this.newAge > 0) {
                this.items.push({ name: this.newName, age: this.newAge })
                this.newName = '';
                this.newAge = null;
            } else {
                alert("Введіть данні коректно")
            }
        },
        addToCounter() {
            this.counter++
        },
        copyItem(){
            return JSON.parse(JSON.stringify(this.items))
        }


    },
    mounted() {
        this.sendApi()
    },
    watch: {
        doubleCounter(firstValue, secondValue) {
            console.log('Зміна значення: нове - ' + firstValue + ', попереднє - ' + secondValue);
        },
        x() {
            this.sendApiRequest()
        },
        'user.age'(value1, value2) {
            console.log('Зміна значення user: нове - ' + value1 + ', попереднє - ' + value2);
        },
        'items.0.age'(newValue, oldValue) {
            console.log('Зміна значення items: нове - ' + newValue + ', попереднє - ' + oldValue);
        },
        
        items: {
            handler(newVal) {
                if(!this.oldItems){
                   this.oldItems = this.copyItem()
                }
                for (let i in newVal) {
                    for (let j in newVal[i]) {
 
                        if (newVal[i][j] !== this.oldItems[i][j]) {
                            console.log(`Old Value: ${this.oldItems[i][j]} , New Value: ${newVal[i][j]}`)
                            return 
                        }

                    }
                }
            },
            deep: true,
            immediate: true
        },
    },
    computed: {
        combinedData() {
            return this.userDataMessage + this.userData
        },
        filteredItems() {
            return this.items.filter(item => item.age < 27);
        },
        sum() {
            return this.items.reduce((accumulator, currentValue) => {
                return accumulator + currentValue.age;
            }, 0);
        },
        firstComputed() {
            return this.secondComputed
        },
        secondComputed() {
            return this.x + 5
        },
        doubleCounter() {
            return this.counter * 2
        }
    }
};

</script>
