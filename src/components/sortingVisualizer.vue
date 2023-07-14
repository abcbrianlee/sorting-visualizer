<template>
    <div class = "button-wrapper">
        <select v-model="selectedOption">
            <option v-for="option in options" :key="option.value" :value="option.value">{{ option.label }} </option>
        </select>
        <p>Selected option: {{ selectedOption }}</p>
        <button @click ="reset">Reset</button>
        <button @click ="randomize">Randomize</button>
        <button @click ="start">Start</button>
    </div>
    <input type="range" v-model.number="currentBars" :min="minBars" :max="maxBars" />
    <p>Amount of bars: {{currentBars}}</p>

    <input type="range" v-model.number="currentDelay" :min="minDelay" :max="maxDelay" />
    <p>Amount of delay: {{currentDelay}}</p>

    <div class ="bar-container">
        <div v-for="(bar,index) in bars"
        :key="index" 
        :class="{ bar: true, highlighted: index <= sortedIndex, compared: index == itemsBeingCompared, compared2: index == itemsBeingCompared2 }"
        :style ="{height: bar*20 + 'px'}"></div>
    </div>
</template>


<script>
export default {
    data () {
        return {
            continueSorting: true,
            sortedIndex: -1,

            options: [
                { label: 'Sort', value: 'Sort'},
                { label: 'Bubble', value: 'Bubble'},
                { label: 'Merge', value: 'Merge'},
                { label: 'Insertion', value: 'Insertion'}

            ],
            selectedOption: '',
            currentBars: 4,
            minBars: 4,
            maxBars: 50,
            bars:[],
            currentDelay: 100,
            minDelay:0,
            maxDelay:500,
        }
    },
    methods: {
        original_display() {
            this.sortedIndex = -1;
            this.itemsBeingCompared = -1;
            this.itemsBeingCompared2 = -1;
            this.bars=[];
            const numbers = Array.from ({length: this.currentBars}, (_, index) => index + 1);
            this.bars = numbers;
        },
        randomize() {
            this.bars = [];
            this.sortedIndex = -1;
            this.itemsBeingCompared = -1;
            this.itemsBeingCompared2 = -1;
            const numbers = Array.from({ length: this.currentBars }, (_, index) => index + 1);
            while (numbers.length > 0) {
                const randomIndex = Math.floor(Math.random() * numbers.length);
                const randomNum = numbers.splice(randomIndex, 1)[0];
                this.bars.push(randomNum);

            }
        },
        reset() {
            this.bars=[]
            this.currentBars = 4;
            this.currentDelay = 100;
    
            this.original_display();

            this.continueSorting = false;
        },
        start() {
            this.continueSorting = true;
            this.sortedIndex = -1;
            if (this.selectedOption == 'Insertion') {
                this.insertionSort(this.bars);
                
            }
            if (this.selectedOption == 'Bubble') {
                this.bubbleSort(this.bars);
            }
        },
        async insertionSort(arr) {
        let delay = this.currentDelay
        for (let i = 1; i < arr.length; i++) {
        let current = arr[i];
        let j = i - 1;
        this.itemsBeingCompared = [i];
        this.itemsBeingCompared2 = [j];
        await new Promise((resolve) => setTimeout(resolve, delay));
        while (j >= 0 && arr[j] > current) {
          await new Promise((resolve) => setTimeout(resolve, delay));
          arr[j + 1] = arr[j];
          j--;
          
          
          await new Promise((resolve) => setTimeout(resolve, delay));
        }
        if (!this.continueSorting) {
            return;
        }
        arr[j + 1] = current;
        this.bars = [...arr];
        this.sortedIndex = i;
        
        await new Promise((resolve) => setTimeout(resolve, delay));

        
        }
        this.isSorting = false;
        return arr;
        },
        async bubbleSort(arr) {
            let delay = this.currentDelay;
            
            for (let i = arr.length - 1; i >= 0; i--) {
                for (let j = 0; j < i; j++) {
                    await new Promise((resolve) => setTimeout(resolve, delay));
                    this.itemsBeingCompared2 = [j + 1];
                    this.itemsBeingCompared = [j];
                    await new Promise((resolve) => setTimeout(resolve, delay));
                    if(arr[j] > arr[j+1]) {
                        
                        let temp = arr[j];
                        arr[j] = arr[j+1];
                        await new Promise((resolve) => setTimeout(resolve, delay));
                        arr[j+1] = temp;
                        
                        await new Promise((resolve) => setTimeout(resolve, delay));
                        
                    }
                    
                }
                
            }
            return arr;
        },

        /*async bubbleSort(arr) {
            let delay = this.currentDelay;
            
            for (let i = arr.length - 1; i >= 0; i--) {
                let lastHighlighted = -1;
                for (let j = 0; j < i; j++) {
                    await new Promise((resolve) => setTimeout(resolve, delay));
                    if(arr[j] > arr[j+1]) {
                        let temp = arr[j];
                        arr[j] = arr[j+1];
                        arr[j+1] = temp;
                        lastHighlighted = j + 1;
                    }
                    this.itemsBeingCompared = j;
                    
                }
                this.itemsBeingCompared2 = lastHighlighted;
                if (lastHighlighted === -1) {
                    // No swaps occurred during this iteration
                    break;
                }
            }
            return arr;
        }*/
    },
    mounted() {
        this.original_display();
    },
    watch: {
    currentBars() {
      this.original_display();
    },

}
}

</script>


<style>
.bar-container{
    display: flex;
    justify-content: space-between;
    align-items: flex-end;
    height: 600px;
    padding: 20px 0;
    background-color: black;

}
.bar{
    flex-grow: 1;
    background-color: blue;
    
    margin: 0 2px 0 2px;
}
.highlighted {
    background-color: white;
}

.compared {
    background-color: red;
}
.compared2 {
    background-color: green;
}

</style>

