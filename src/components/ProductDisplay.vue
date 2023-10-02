<template>
    <div class="container"
        :class="isLoading ? 'bg-white' : (product.data.category === 'men\'s clothing' ? 'bg-men' : 'bg-women')">
        
        <!-- display loading when data is fetching -->
        <div v-if="isLoading">
            <div class="product-container-spinner">
                <div class="spinner"></div>
            </div>
        </div>

        <!-- display product info -->
        <div v-else class="container"
            :class="!isProductAvailable ? 'bg-none' : product.data.category === 'men\'s clothing' ? 'bg-men' : 'bg-women'">
            <div class="overlay"></div>
            <div class="card">
                <div v-if="!isProductAvailable" class="product-unavailable-container">
                    <div class="product-details">
                        <p>This product is not available.</p>
                        <div class="cta">
                            <button type="button" @click="getSingleProduct()" class="cta-next-none">Next Product</button>
                        </div>
                    </div>
                </div>
                <div v-else class="product-container">
                    <div class="product-thumbnail">
                        <img :src="product.data.image" alt="" class="product-image">
                    </div>
                    <div class="product-details">
                        <div class="top">
                            <h3 :class="product.data.category === 'men\'s clothing' ? 'font-men' : 'font-women'"
                                class="title">{{ product.data.title }}</h3>
                            <div class="sub-title">
                                <span>{{ product.data.category }}</span>
                                <div class="rating">
                                    <span>{{ product.data.rating.rate }}/5</span>
                                    <div class="rating">
                                        <span v-for="i in 5" :key="i"
                                            :class="['circle', i <= product.data.rating.rate ? ratingColorClass : '']"></span>
                                    </div>
                                </div>
                            </div>
                            <div class="description">
                                <p>{{ product.data.description }}</p>
                            </div>
                        </div>
                        <div class="bottom">
                            <span :class="product.data.category === 'men\'s clothing' ? 'font-men' : 'font-women'"
                                class="price">${{ product.data.price }}</span>
                            <div class="cta">
                                <button type="button"
                                    :class="product.data.category === 'men\'s clothing' ? 'cta-buy-men' : 'cta-buy-women'"
                                    class="cta-buy">Buy Now</button>
                                <button type="button" @click="getSingleProduct()"
                                    :class="!isProductAvailable ? 'cta-next-none' : product.data.category === 'men\'s clothing' ? 'cta-next-men' : 'cta-next-women'">Next
                                    Product</button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'ProductDisplay',
    data() {
        return {
            /* return fetched data */ 
            isLoading: false, 
            index: 0,
            isProductAvailable: false,
            product: {}
        }
    },
    computed: {
        /* compute rating circle color based on category */
        ratingColorClass() {
            return this.product.data.category === "men's clothing" ? 'bg-rating-men' : 'bg-rating-women';
        },
    },
    /* fetching method from API */
    methods: {
        async callAPI() {
            const response = await fetch(`https://fakestoreapi.com/products/${this.index}`);
            const result = await response.json();
            return result;
        },
        /* fetch single product, and then update the state */
        async getSingleProduct() {
            this.isLoading = true;
            /* increment for next product until product number 20, and then reset after 20 */
            if (this.index !== 20) {
                this.index++
            } else {
                this.index = 1;
            }

            let data = await this.callAPI()
            if (data.category === "men's clothing" || data.category === "women's clothing") {
                this.product = { data }
                this.isProductAvailable = true;
            } else {
                this.isProductAvailable = false;
            }

            this.isLoading = false;
        }
    },
    mounted() {
        this.getSingleProduct();
    },
}
</script>



<style scoped>
* {
    margin: 0;
    box-sizing: border-box;
    font-family: 'Arial', sans-serif;
}

.container {
    position: relative;
    width: 100%;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
}

/* white overlay for the background behind the card  */
.overlay {
    position: absolute;
    z-index: 1;
    top: 50%;
    width: 100%;
    height: 50%;
    background: linear-gradient(to bottom, #ffffff 50%, #ffffff 50%);
}

.card {
    display: flex;
    position: relative;
    z-index: 2;
    width: 80%;
    height: 70vh;
    background-color: white;
    border-radius: 10px;
    padding: 48px 32px;
    box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);
}

/* unavailable product */
.product-unavailable-container {
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
}

.product-unavailable-container>.product-details {
    text-align: center;
    z-index: 2;
}

.product-unavailable-container .cta-next {
    margin: 0px auto;
    border: 2px solid black;
    color: black;
    border-radius: 7px;
}

/* available product informations*/
.product-container {
    width: 100%;
    display: grid;
    grid-template-columns: 30% 65%;
    gap: 16px;
    height: 100%;
}

.product-thumbnail {
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
}

.product-thumbnail>img {
    width: 60%;
}

.product-details {
    display: flex;
    width: 100%;
    flex-direction: column;
    justify-content: space-between;
}

.product-details .title {
    font-size: 32px;
    font-weight: 600;
}

.product-details .sub-title {
    display: flex;
    width: 100%;
    justify-content: space-between;
    margin-top: 14px;
    font-size: 16px;
    color: black;
    padding-bottom: 16px;
    border-bottom: 1px solid gray;
}

.product-details .rating {
    display: flex;
    gap: 3px;
    align-items: center;
    padding-left: 10px;
}

.circle {
    display: block;
    width: 14px;
    height: 14px;
    border-radius: 100%;
    background-color: #DCDCDC;

}

.font-men {
    color: #002772;
}

.font-women {
    color: #720060;
}

.bg-rating-men {
    background-color: #002772;
}

.bg-rating-women {
    background-color: #720060;
}

.product-details .description {
    width: 100%;
    margin-top: 24px;
    font-size: 14px;
    color: black;
}

.product-details>.bottom {
    padding-top: 16px;
    border-top: 1px solid gray;
}

.product-details .price {
    font-size: 32px;
    font-weight: 600;
}

/* buttons */
.cta {
    margin-top: 16px;
    width: 100%;
    display: flex;
    gap: 12px;
}

.cta>.cta-buy-men {
    width: 25%;
    height: 36px;
    font-size: 16px;
    border-radius: 7px;
    font-weight: bold;
    cursor: pointer;
    border: none;
    background-color: #002772;
    color: #FFFFFF;
}

.cta>.cta-buy-women {
    width: 35%;
    height: 36px;
    font-size: 16px;
    border-radius: 7px;
    font-weight: bold;
    cursor: pointer;
    border: none;
    background-color: #720060;
    color: #FFFFFF;
}

.cta>.cta-next-men {
    background-color: #fff;
    color: #002772;
    border: solid 2px #002772;
    width: 25%;
    height: 36px;
    font-size: 16px;
    border-radius: 7px;
    font-weight: bold;
    cursor: pointer;

}

.cta>.cta-next-women {
    background-color: #fff;
    color: #720060;
    border: solid 2px #720060;
    width: 25%;
    height: 36px;
    font-size: 16px;
    border-radius: 7px;
    font-weight: bold;
    cursor: pointer;

}

.cta>.cta-next-none {
    background-color: #fff;
    color: #000;
    border: solid 1px #000;
    margin: 0px auto;
    width: 25%;
    height: 36px;
    font-size: 16px;
    border-radius: 7px;
    font-weight: bold;
    cursor: pointer;
}

.bg-men {
    background: #D6E6FF;
    border-radius: 5px;

}

.bg-women {
    background: #FDE2FF;
    border-radius: 5px;
}

.bg-none {
    background: #DCDCDC;
    border-radius: 5px;
}

/* spinner styles */
.spinner {
    border: 4px solid rgba(255, 255, 255, 0.3);
    border-top: 4px solid #333;
    border-radius: 50%;
    width: 40px;
    height: 40px;
    animation: spin 2s linear infinite;
    margin: 20px auto;
}

.bg-spinner {
    background: #fff;
}

@keyframes spin {
    0% {
        transform: rotate(0deg);
    }

    100% {
        transform: rotate(360deg);
    }
}</style>

