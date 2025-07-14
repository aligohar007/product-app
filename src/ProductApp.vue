<template>
    <h2 style="text-align: center; font-family: Arial, Helvetica, sans-serif;">Products Search App</h2>
    <div class="input-container">
        <input class="styled-input" v-model="searchtitle" @keyup.enter="SearchProduct" placeholder="Enter Title..." />

        <button class="Searchbtn" @click="SearchProduct">Search</button>
    </div>

    <h1 style="font-family: Arial, Helvetica, sans-serif; text-align: center;" v-if="loading">Loading...</h1>
    <div v-if="loading" class="spinner-container">
        <div class="spinner"></div>
    </div>

    <h1 style="text-align: center; font-family: Arial, Helvetica, sans-serif;">{{ error }}</h1>

    <ul class="setlist" v-if="!loading && !error">
        <li v-for="(product, index) in filteredProducts" :key="index" style="list-style: none; padding: 10px; border :
       1px solid #ccc; border-radius: 8px; margin-bottom: 10px; width: 50%;">
            <strong>ID:</strong> {{ product.id }} <br />
            <strong>Title:</strong> {{ product.title }} <br />
            <strong>Price:</strong> ${{ product.price }} <br />
            <strong>Image:</strong><br />
            <img :src="product.image" alt="Product Image" style="width: 100px;" /> <br />
            <strong>Description:</strong> {{ product.description }}
        </li>
    </ul>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const productdata = ref([]);
const loading = ref(true);
const error = ref(null);
const searchtitle = ref('');
const filteredProducts = ref([]);

const fetchdata = async () => {
    try {
        const response = await fetch('https://fakestoreapi.com/products');
        if (!response.ok) {
            throw Error('Something went wrong');
        }
        productdata.value = await response.json();
        filteredProducts.value = productdata.value.filter(p => p.id <= 8);
    } catch (err) {
        console.log('Error:', err);
        error.value = err.message;
    } finally {
        loading.value = false;
    }
};
// Function to search products based on title
const SearchProduct = () => {
    const title = searchtitle.value.trim().toLowerCase();
    loading.value = true;
    error.value = null;

    if (title.length > 30) {
        error.value = 'Title must be less than or equal to 20 characters';
        loading.value = false;
        return;
    } else if (title.length === 0) {
        error.value = 'Title cannot be empty';
        loading.value = false;
        return;
    }

    setTimeout(() => {
        filteredProducts.value = productdata.value.filter((p) =>
            p.title.toLowerCase().includes(title)
        );
        loading.value = false;
    }, 500);
};
onMounted(() => {
    setTimeout(() => {
        fetchdata();
    }, 1000);
});
</script>



<style scoped>
.input-container {
    display: flex;
    justify-content: center;
    margin-bottom: 20px;
    position: relative;
    width: 320px;
    margin: 40px auto;
}

.styled-input {
    padding: 10px 10px 10px 20px;
    width: 100%;
    border: 2px solid #ccc;
    border-radius: 8px;
    font-size: 16px;
    outline: none;
    transition: 0.3s;
    box-sizing: border-box;
}

.styled-input:focus {
    border-color: #007bff;
    box-shadow: 0 0 5px rgba(0, 123, 255, 0.5);
    background-color: #f0f8ff;
}

.Searchbtn {
    position: absolute;
    right: 2px;
    top: 50%;
    transform: translateY(-50%);
    padding: 12px 18px;
    background-color: #007bff;
    color: white;
    border: none;
    border-radius: 3%;
    cursor: pointer;
    font-size: 14px;
    transition: background-color 0.3s;
}

.Searchbtn:hover {
    background-color: #0056b3;
}

.setlist {
    list-style: none;
    padding: 0;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 80%;
}

.spinner-container {
    display: flex;
    justify-content: center;
    margin: 20px;
}

.spinner {
    border: 6px solid #f3f3f3;
    border-top: 6px solid #3498db;
    border-radius: 50%;
    width: 40px;
    height: 40px;
    animation: spin 1s linear infinite;
}

/* Rotate the spinner  */
@keyframes spin {
    0% {
        transform: rotate(0deg);
    }

    100% {
        transform: rotate(360deg);
    }
}

/* Media query start */

@media screen and (max-width: 600px) {
    .input-container {
        width: 90%;
    }

    .styled-input {
        padding: 10px;
        font-size: 14px;
    }

    .Searchbtn {
        padding: 10px;
        font-size: 12px;
    }

    .setlist li {
        width: 100%;
        padding: 15px;
        font-size: 14px;
    }

    h2 {
        font-size: 20px;
    }

    .setlist {
        width: 100%;
        padding: 15px;
        font-size: 14px;
    }

    h1 {
        font-size: 18px;
    }

    p {
        font-size: 16px;
    }

    .Searchbtn {
        padding: 10px 15px;
        font-size: 14px;
    }
}
</style>
