<template>
    <div class="container">
        <div class="main-box">
            <div class="input-list">
                <input class="input-item" @input="priceField" type="number" placeholder="Цена" :value="price">
                <input class="input-item" @input="qtyField" type="number" placeholder="Кол-во" :value="qty">
                <input class="input-item" @input="amountField" type="number" placeholder="Сумма" :value="amount">
                <button class="input-item btn" @click="activate">Расчёт</button>
            </div>
            <div class="label-list">
                <label for="">Цена: {{ price || 0 }}</label>
                <label for="">Кол-во: {{ qty || 0 }}</label>
                <label for="">Итого: {{ amount || 0 }}
                    <div>
                        {{ is_even.success ? 'Четное:' : 'Нечетное:' }} <code>{{ is_even }}</code>
                    </div>
                </label>
                <label for="">localStorage :
                    <div>
                        <code>{{ localData || 'No data' }}</code>
                    </div>
                </label>
            </div>
            <div class="main-actions">
                <div class="main-list" v-if="action_list.length > 0">
                    <div class="main-item" v-for="(item, index) in action_list" :key="index">
                        <span>{{ index + 1 }}</span>{{ item }}
                    </div>
                </div>
                <div class="main-list" v-else>Нет событий</div>
            </div>
        </div>
    </div>
</template>
  
<script>
export default {
    name: 'MainPage',
    data() {
        return {
            price: 0,
            qty: 0,
            amount: 0,
            nonce: 0,
            localData: {},
            action_list: [],
            is_even: {},
            debounce: null
        }
    },
    created() {
        this.init()
    },
    watch: {
        price(newVal){
            this.amount = this.qty * newVal
        },
        qty(newVal){
            this.amount = this.price * newVal
        },
        amount(newVal){
            this.price = (newVal / this.qty).toFixed(2)
        },
    },
    methods: {
        init() {
            if (localStorage.getItem("dataStorage")) {
                this.localData = JSON.parse(localStorage.getItem("dataStorage"))
                this.price = this.localData.price
                this.qty = this.localData.qty
                this.amount = this.localData.amount
                this.nonce = this.localData.nonce
            } else {
                localStorage.setItem("dataStorage", '')
            }
        },
        activate() {
            this.nonce += 1
            const dataStorage = {
                nonce: this.nonce,
                price: this.price,
                qty: this.qty,
                amount: this.amount,
            }

            setTimeout(() => this.setResult(dataStorage), 1000);
        },
        setResult(dataStorage) {
            this.is_even = {
                success: true
            }
            if (this.amount % 2 == 0) {
                this.is_even.success = true
                localStorage.setItem("isEven", JSON.stringify(this.is_even))
                localStorage.setItem("dataStorage", JSON.stringify(dataStorage))
                this.actionList(`Четное(${this.amount}), записываю в локалсторедж`)
            }
            else {
                this.is_even.success = false
            }
            this.localData = JSON.parse(localStorage.getItem("dataStorage"))
        },
       
        priceField(event) {
            this.debounceAction(event.target.value, 'price')
        },
        qtyField() {
            this.debounceAction(event.target.value, 'qty')
        },
        amountField() {
            this.debounceAction(event.target.value, 'amount')
        },
        actionList(act) {
            this.action_list.push(`${act}`)
        },
        debounceAction(target, inp) {
            clearTimeout(this.debounce)
            this.debounce = setTimeout(() => {
                switch (inp) {
                    case 'price':
                        this.price = target
                        break
                    case 'qty':
                        this.qty = target
                        break
                    case 'amount':
                        this.amount = target
                        break
                }
                this.actionList(`Изменение  поля: ${inp} на ${target || 0}`)
            }, 300)
        }
    },
}
</script>
  
<style scoped lang="scss">
.container {
    max-width: 900px;
    width: 100%;
    margin: 0 auto;
    background: rgb(237, 237, 237);
    min-height: 400px;
    border-radius: 25px;
    padding: 20px 40px;
    box-shadow: 4px 4px 8px 0px rgba(34, 60, 80, 0.2);
}

.input-list {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.input-item {
    max-width: 200px;
    width: 100%;
    height: 70px;
    padding: 5px 10px;
    border-radius: 35px;
    font-size: 20px;
    text-align: center;
    &.btn {
        cursor: pointer;
        transition: 0.5s;
        background: #00b894;
        color: white;
        &:hover {
            background: #55efc4;
        }
    }
}

.label-list {
    display: flex;
    justify-content: space-between;
    align-items: center;
    min-height: 140px;

    label {
        text-align: center;
        max-width: 200px;
        width: 100%;
    }
}

.main-actions {
    min-height: 350px;
    background: white;
    margin-top: 20px;
    border-radius: 25px;
    padding: 20px;
}

.main-list {
    display: flex;
    flex-direction: column-reverse;

    .main-item {
        margin: 10px 0;
        background: #a29bfe;
        padding: 10px 20px;
        border-radius: 20px;
        font-size: 20px;
        color: white;
        position: relative;

        span {
            position: absolute;
            background: #fd79a8;
            width: 35px;
            height: 35px;
            border-radius: 50%;
            line-height: 35px;
            left: 15px;
            top: 50%;
            transform: translateY(-50%);

        }
    }
}
</style>
  