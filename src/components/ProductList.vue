<template>
    <div class="product-list">
        <center><h1>Product List</h1></center>
        <hr>
        <table class="item-list">
            <thead>
                <tr>
                    <th>Item</th>
                    <th>Price</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>Pencil</td>
                    <td>₱8</td>
                </tr>
                <tr>
                    <td>Ballpen</td>
                    <td>₱10</td>
                </tr>
                <tr>
                    <td>Marker</td>
                    <td>₱15</td>
                </tr>
                <tr>
                    <td>Bond Paper</td>
                    <td>₱1</td>
                </tr>
                <tr>
                    <td>Sticky Notes</td>
                    <td>₱10</td>
                </tr>
                <tr>
                    <td>Yellow Pad</td>
                    <td>₱25</td>
                </tr>
                <tr>
                    <td>Notebook</td>
                    <td>₱32</td>
                </tr>
                <tr>
                    <td>Eraser</td>
                    <td>₱5</td>
                </tr>
                <tr>
                    <td>Colored Paper</td>
                    <td>₱2</td>
                </tr>
                <tr>
                    <td>Scissor</td>
                    <td>₱28</td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>

export default {
    components: {
    },
    data() {
        return {
            items: [
                { name: 'Pencil', cost: 8 },
                { name: 'Ballpen', cost: 10 },
                { name: 'Marker', cost: 15 },
                { name: 'Bond Paper', cost: 1 },
                { name: 'Sticky Notes', cost: 10 },
                { name: 'Yellow Pad', cost: 25 },
                { name: 'Notebook', cost: 32 },
                { name: 'Eraser', cost: 5 },
                { name: 'Colored Paper', cost: 2 },
                { name: 'Scissor', cost: 28 }
            ],
            cart: []
        };
    },
    methods: {
    
    },
};
</script>

<style>
.product-list {
  max-width: 600px;
  margin: 0 auto;
}

.item-list{
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}

.item-list th{
  text-align: left;
  padding: 8px;
  background-color: #f2f2f2;
}

.item-list td{
  padding: 8px;
}

</style>
