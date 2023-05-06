<template>
<!-- Корзина  -->
<div id="cart_wrapper">
    <div class="cart" v-if="this.cartLength > 0">
        <p>🙂 В корзине {{ totalAmount }} позиций на сумму {{ totalPrice }} руб.</p>
    </div>
    <div class="cart" v-else>
        <p>😥 Корзина пуста</p>
    </div>
</div>

<!-- Таблица товаров -->
<div id="main_wrapper">
    <div id="pre" v-for="(product, index) in products" :key="index">
        <div class="product">
            <p class="name">{{ product.name }}</p>
            <p class="img" :id="'img_' + index"><img :src=product.img alt=""></p>
            <p class="price">{{ product.price }} руб.</p> 
            <button :id="'button_addcart_' + index" @click="addcart(product, index)">В корзину</button>
            <div :id="'block_buttons_' + index" class="hide button_amount">
                <button :id="'button_addcart_plus_' + index" @click="addcartPlus(product, index)">+</button>
                &ensp;
                <button :id="'button_addcart_minus_' + index" @click="addcartMinus(product, index)">-</button>
            </div>
            <div v-for="el in cart" id="notice_cart">
                <div v-if="el.name == product.name && el.amount != 0" class="notice_cart">
                    {{ el.amount }}
                </div>
            </div>
        </div>
    </div>
    <div v-if="this.cartLength > 0">
    <button class="btn_cart" @click="checkout">Оформить заказ</button>
    </div>
</div>

<!-- Страница "Мой заказ" -->
<div id="order_wrapper" class="hide">
    <div class="order">
    <h1>Мой заказ</h1>
    <div v-for="el in cart" id="order_table">
        <div class="order_table">
            <p class="images"><img width=30 :src=el.img alt=""></p> 
            <p class="name">{{ el.name }}</p> 
            <p class="amount">&ensp;x {{ el.amount }}</p> 
            <p class="price">{{ el.price }} руб.</p> 

        </div>
    </div>
    <div class="order_table total_table">
        <p>Итого:</p> <p class="total">{{ totalPrice }} руб.</p>
    </div>
    </div>
    <div class="order">
        <h4>Комментарий к заказу</h4>
        <input type="text">
    </div>
</div>

</template>
      
<script>
import { ref, computed } from 'vue'
export default {
    name: 'Main',
    setup() {
        var tg = window.Telegram.WebApp;
        var cart = ref({})
        function addcart(product, index) {
            this.cart[index] = {"name": product.name, "price": product.price, "img": product.img, "amount": 1}
            var button = document.getElementById('button_addcart_' + index)
            button.style.display = "none";
            var block_buttons = document.getElementById('block_buttons_' + index)
            block_buttons.style.display = "flex";
            block_buttons.style.justifyContent = "center";
            var img = document.getElementById('img_' + index)
            img.classList.add('img_animate');
            setTimeout(() => {
                img.classList.add('img_animate_after');
            }, 500);
            setTimeout(() => {
                img.classList.remove('img_animate_after');
                img.classList.remove('img_animate');
            }, 1000);
        }

        function addcartPlus(product, index) {
            for (let productId in this.cart) {
                let prod = this.cart[productId];
                if (product.name == prod.name && prod.amount < 99) {
                    prod.amount += 1
                }
            }
        }

        function addcartMinus(product, index) {
            for (let productId in this.cart) {
                let prod = this.cart[productId];
                if (product.name == prod.name && prod.amount != 1) {
                    prod.amount -= 1
                }
                else if (product.name == prod.name && prod.amount == 1) {
                    delete this.cart[productId];
                    var button = document.getElementById('button_addcart_' + index)
                    button.style.display = "block";
                    var block_buttons = document.getElementById('block_buttons_' + index)
                    block_buttons.style.display = "none";
                }
            }
        }

        function checkout() {
            var main = document.getElementById('main_wrapper')
            var cart = document.getElementById('cart_wrapper')
            var order = document.getElementById('order_wrapper')
            main.style.display = "none";
            cart.style.display = "none";
            order.classList.remove('hide')
            if (tg.MainButton.isVisible) {
                tg.MainButton.hide();
            } else {
                tg.MainButton.setText(`Оформить заказ`);
                tg.MainButton.show();
            }
            Telegram.WebApp.onEvent("mainButtonClicked", function(){
                tg.openInvoice("https://pay.telegram.org/0/invoice/AAABC1234-1234-1234-1234-123412341234", onInvoiceClose);
                tg.MainButton.setText(`Оплачено`);
            })
        }

        return {
            cart,
            addcart,
            addcartPlus,
            addcartMinus,
            cartLength: computed(() => Object.keys(cart.value).length),
            checkout,
        }
    },
    data() {
        return {
            products: {
                product_1: {name: 'Биг Маэстро Бургер', price: 269, img: 'https://i.ibb.co/JvzZnQc/1.png'},
                product_2: {name: 'Картошка фри Соломка', price: 78, img: 'https://i.ibb.co/2WV1z68/2.png'},
                product_3: {name: 'Чикен Бургер', price: 124, img: 'https://i.ibb.co/N63ps60/3.png'},
                product_4: {name: 'Пицца Роялити', price: 349, img: 'https://i.ibb.co/LSHnF1t/4.png'},
                product_5: {name: 'Рол Твистер ДеЛюкс', price: 199, img: 'https://i.ibb.co/2MDJz4H/5.png'},
                product_6: {name: 'ХотДог Чикаго', price: 95, img: 'https://i.ibb.co/zVfCMV6/6.png'},
                product_7: {name: 'Жаренная курочка', price: 250, img: 'https://i.ibb.co/vjQ74nx/7.png'},
                product_8: {name: 'Картошка фри Колечки', price: 87, img: 'https://i.ibb.co/G5y0ZrJ/8.png'},
                product_9: {name: 'Пирамидка МайКарри', price: 134, img: 'https://i.ibb.co/MnxD3SB/9.png'},
                product_10: {name: 'Боксмастер Айтвистер', price: 159, img: 'https://i.ibb.co/58P0LbK/10.png'},
                product_11: {name: 'Лонгер Ориджинал', price: 65, img: 'https://i.ibb.co/gyKP6Gz/11.png'}
            },
        }
    },
    computed: {
        totalPrice() {
            let total = 0;
            for (let productId in this.cart) {
                let prod = this.cart[productId];
                total += prod.price * prod.amount;
            }
            return total;
        },

        totalAmount() {
            let total = 0;
            for (let productId in this.cart) {
                let prod = this.cart[productId];
                total += prod.amount;
            }
            return total;
        }
}
}
</script>

<style>
#main_wrapper {
    display: flex;
    flex-wrap: wrap;
    justify-content:space-between;
    position: relative;
    padding-bottom: 70px;
}

h1 {
    padding: 0 0 20px 0
}

#order_wrapper {
    padding: 15px;
    height: 100vh;
    
}

#order_wrapper .order {
    background-color: #e9e9e9;
    padding: 10px;
    border-radius: 10px;
    margin-bottom: 10px;
}

.order input {
    border: 1px solid silver;
    border-radius: 5px;
    outline: none;
    width: 100%;
    height: 30px;
    padding: 0 5px;
    box-sizing: border-box;
    margin-top: 10px;
}

#pre:nth-child(2n+1) {margin: 0 0 15px 15px;}
#pre:nth-child(2n+2) {margin: 0 15px 15px 0;}
#pre:nth-child(-n+2) {margin-top: 15px;}

.product {
    text-align: center;
    width: 152px;
    background-color: #e9e9e9;
    padding: 10px;
    border-radius: 10px;
    position: relative;
}

.product .name {
    font-size: 15px;
    font-weight: bold;
    width: 70%;
    padding: 0 15%;
    min-height: 50px;
    display: flex;
    align-items: center;
}

.product .img img {
    width: 90px;
    padding: 15px 0;
}

.product .price {
    font-weight: bold;
}

.product button{
    border: 0px;
    border-radius: 5px;
    width: 100%;
    background-color: green;
    color: white;
    padding: 10px;
    margin: 10px 0 0 0;
}
.product button:hover{
    background-color: #ff7b00;
    cursor: pointer;
}

.hide {
    display: none;
}

.button_amount button {
    font-size: 18px;
    font-weight: bold;
    line-height: 17px;
    width: 40px;
}

#notice_cart {
    position: absolute;
    top: -4px;
    right: -4px;
}

.notice_cart {
    background-color: #ff7b00;
    border: 6px solid #ffffff;
    color: white;
    padding: 2px 8px;
    border-radius: 50px;
    z-index: 5;
}

.cart {
    padding: 15px 15px 0 15px;
}

.btn_cart {
    position: fixed;
    bottom: -100px;
    left: 0px;
    border: 0px;
    width: 390px;
    height: 70px;
    background-color: rgb(0, 102, 255);
    color: white;
    font-size: 16px;
    margin-top: 50px;
    animation-name: btncartanimate;
    animation-duration: .5s;
    animation-fill-mode: both;
}

.btn_cart:hover {
    background-color: rgb(1, 63, 156);
    cursor: pointer;
}

.img_animate {
    display: block;
    animation-name: imganimate;
    animation-duration: .5s;
    animation-fill-mode: both;
}

.img_animate_after {
    display: block;
    animation-name: imganimate_after;
    animation-duration: .5s;
    animation-fill-mode: both;
}

@keyframes imganimate {
    0% {transform: scale(100%, 100%) translate(0px, 0px); opacity: 1;}
    100% {transform: scale(50%, 50% ) translate(150px, -230px); opacity: 0;}
}

@keyframes imganimate_after {
    0% {transform: rotate(0deg) scale(0);}
    100% {transform: rotate(720deg) scale(1);}
}

@keyframes btncartanimate {
    0% {bottom: -100px;}
    100% {bottom: 0px;}
}

.order_table {
    display: flex;
    align-items: center;
    margin-bottom: 10px;
    width: 100%;
}

.order_table .images {
   background-color: rgb(255, 255, 255);
   border-radius: 50px;
   width: 40px;
   height: 40px;
   display: flex;
   justify-content: center;
   align-items: center;
   margin-right: 10px;
}

.order_table .name{
    display: block;
}

.order_table .amount {
    color: #ff7b00;
    font-weight: bold;
    justify-self: flex-start;
}

.order_table .price {
    margin-left: auto;
}

.total {
    margin-left: auto;
}

.total_table {
    font-weight: bold;
    border-top: 1px solid #bebebe;
    padding-top: 10px;
    margin-top: 30px;
}



</style>