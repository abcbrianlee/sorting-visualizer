<template>
    <div class = "app-container">
        <div class ="section-container">
            <div class="left-section">
                <p>Info:{{ getDescription(selectedOption) }}</p>
            </div>
            <div class = "button-wrapper">
                <h1>Sorting Algorithm Visualizer</h1>
                <select v-model="selectedOption">
                    <option v-for="option in options" :key="option.value" :value="option.value">{{ option.label }} </option>
                </select>
                <p>Selected option: {{ selectedOption }}</p>
                <button @click ="reset">Reset</button>
                <button @click ="randomize">Randomize</button>
                <button @click ="start">Start</button>
                <br>
                <input type="range" v-model.number="currentBars" :min="minBars" :max="maxBars" />
                <h5>Amount of bars: {{currentBars}}</h5>

                <input type="range" v-model.number="currentDelay" :min="minDelay" :max="maxDelay" />
                <h5>Amount of delay: {{currentDelay}}</h5>
            </div>
        </div>
        <div class ="bar-container">
            <div v-for="(bar,index) in bars"
            :key="index" 
            :class="{ bar: true, 
                highlighted: index >= bubbleSortedIndex,
                insertionHighlight: index < sortedIndex,
                bubbleCompare: index == itemsBeingBubbleCompared,
                bubbleCompare2: index == itemsBeingBubbleCompared2,
                compared: index == itemsBeingCompared, 
                compared2: index == itemsBeingCompared2,
                selectionCompare: index == itemsBeingSelectionCompared,
                selectionCompare2: index == itemsBeingSelectionCompared2,
                selectionCompare3: index == itemsBeingSelectionCompared3,
                selectionCompare4: index < itemsBeingSelectionCompared4
                }"
            :style ="{height: bar*20 + 'px'}"></div>
        </div>
    </div>
</template>


<script>
export default {
    data () {
        return {
            options: [
                { label: 'Bubble', value: 'Bubble'},
                { label: 'Merge', value: 'Merge'},
                { label: 'Insertion', value: 'Insertion'},
                { label: 'Selection', value: 'Selection'}
                ],
            info: {
                Bubble: 'This is bubble sort. Bubble sort is a basic sort and is very ineffecient',
                Merge: 'This is merge sort',
                Insertion: 'This is insertion sort',
                Selection: 'This is selection sort'
            
            },
            continueSorting: true,
            sortedIndex: -1,
            selectedOption: '',
            currentBars: 4,
            minBars: 4,
            maxBars: 31,
            bars:[],
            currentDelay: 100,
            minDelay:0,
            maxDelay:500,
            itemsBeingBubbleCompared: [-1],
            itemsBeingBubbleCompared2: [-1],
            itemsBeingCompared: [],
            itemsBeingCompared2: [],
            itemsBeingSelectionCompared: [-1],
            itemsBeingSelectionCompared2: [-1],
            combined: [],
        }
    },
    methods: {
        getDescription(option) {
            if (!option) {
                return "Welcome to the sorting algorithm visualizer. To get started, please select a sorting algorithm from the drop down box.  Once you have selected a sorting algorithm, you can modify the amount of bars as well as the delay to speed up or slow down the process.  Once you have made your selection, you can randomize the bars. Finally you are ready to start!"
            }
            return this.info[option] || '';
        },
        original_display() {
            this.reset_items();
            this.bars=[];
            const numbers = Array.from ({length: this.currentBars}, (_, index) => index + 1);
            this.bars = numbers;
        },
        reset_items() {
            this.sortedIndex = -1;
            this.bubbleSortedIndex = 500;
            this.itemsBeingCompared = -1;
            this.itemsBeingCompared2 = -1
            this.itemsBeingBubbleCompared = -1;
            this.itemsBeingBubbleCompared2 = -1;
            this.itemsBeingSelectionCompared = -1;
            this.itemsBeingSelectionCompared2 = -1;
            this.itemsBeingSelectionCompared3 = -1;
            this.itemsBeingSelectionCompared4 = -1;

            
        },
        randomize() {
            this.reset_items();
            this.bars = [];
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
            if (this.selectedOption =='Selection') {
                this.selectionSort(this.bars);
            }
            if (this.selectedOption == 'Merge') {
                this.mergeSort(this.bars);
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
        console.log(this.itemsBeingCompared);
        console.log(this.itemsBeingCompared2);
        return arr;
        },
        async bubbleSort(arr) {
            let delay = this.currentDelay;
            let bubbleSortedIndex = arr.length;
            for (let i = arr.length - 1; i >= 0; i--) {
                for (let j = 0; j < i; j++) {
                    await new Promise((resolve) => setTimeout(resolve, delay));
                    if (!this.continueSorting) {
                        this.itemsBeingBubbleCompared = -1;
                        this.itemsBeingBubbleCompared2 = -1;
                        
                        return;
                     }
                    this.itemsBeingBubbleCompared = [j];
                    this.itemsBeingBubbleCompared2 = [j + 1];
                    await new Promise((resolve) => setTimeout(resolve, delay));
                    if(arr[j] > arr[j+1]) {
                        
                        let temp = arr[j];
                        arr[j] = arr[j+1];
                        await new Promise((resolve) => setTimeout(resolve, delay));
                        arr[j+1] = temp;
                        await new Promise((resolve) => setTimeout(resolve, delay));
                        this.itemsBeingBubbleCompared = [j + 1];
                        this.itemsBeingBubbleCompared2 = [j];
                        await new Promise((resolve) => setTimeout(resolve, delay));
                        
                    } 
                    
                }
                bubbleSortedIndex--;
                this.bubbleSortedIndex = bubbleSortedIndex;
            }
            this.itemsBeingBubbleCompared = -1;//Resets markers so index 0 doesn't stay orange
            this.itemsBeingBubbleCompared2 = -1;//Resets markers so index 1 doesn't stay purple
            
        },
        async selectionSort(arr) {
            let delay = this.currentDelay;
            await new Promise((resolve) => setTimeout(resolve, delay));          
            for (let i = 0; i < arr.length; i ++) {
                this.itemsBeingSelectionCompared4 = [i]
                await new Promise((resolve) => setTimeout(resolve, delay));
                this.itemsBeingSelectionCompared3 = [-1];
                let minIndex = i;
                this.itemsBeingSelectionCompared = [i]
                await new Promise((resolve) => setTimeout(resolve, delay));
                for (let j = i + 1; j < arr.length; j++) {
                    await new Promise((resolve) => setTimeout(resolve, delay));

                    this.itemsBeingSelectionCompared2 = [j]
                    if (arr[j] < arr[minIndex]) {
                        minIndex = j;
                        this.itemsBeingSelectionCompared3 = [j];
                    }
                }
                if (i !== minIndex) {
                    let temp = arr[i];
                    arr[i] = arr[minIndex];
                    arr[minIndex] = temp;
                }
            }
            this.itemsBeingSelectionCompared2 = [-1];
            this.itemsBeingSelectionCompared = [-1]
            this.itemsBeingSelectionCompared4 = arr.length
            return arr;
        },
        mergeSort(arr) {
            
            if (arr.length === 1  ) {
                return arr;
            }
            const midIndex = Math.floor(arr.length / 2);
            const left = this.mergeSort(arr.slice(0, midIndex));
            const right = this.mergeSort(arr.slice(midIndex));
            console.log(left)
            console.log(right)
            return this.merge(left, right);
            //const merged = this.merge(left, right);
            //this.bars = merged;
            //return merged;
        },
        merge(list1, list2) {

            let combined = [];
            let i = 0;
            let j = 0;
            
            while (i < list1.length && j < list2.length) {

                if (list1[i] < list2[j]) {
                    combined.push(list1[i]);
                    i++;
                } else {
                    combined.push(list2[j]);
                    j++;
                }
            }
            
            while (i < list1.length) {
                combined.push(list1[i]);
                i++;
            }
            
            while (j < list2.length) {
                combined.push(list2[j]);
                j++;
            }
            for (let k = 0; k < combined.length; k++) {
                 this.bars[k] = combined[k]; // Update the bars array with the sorted result
            }
            console.log(combined)
            return combined;
            
        },
        },
        

    

    mounted() {
        this.original_display();
    },
    watch: {
    currentBars() {
      this.original_display();
    },

    },  
    
}


</script>


<style>
.app-container {
    height: 100vh;
    width: 100vw;
    display: flex;
    flex-direction: column;
}
.section-container {
    display: flex;
    flex-direction: row;
    justify-content: center;
  
}
.left-section {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-basis: 10%;
    margin-left: auto;
    
}
.button-wrapper {
    margin: auto;
    height: 100%;
    flex-basis: 70%;
    align-self: center;
}
.bar-container{
    display: flex;
    align-items: flex-end;
    height: 60%;
    padding: 10px 0;
    background-color: black;
    box-sizing: border-box;

}
.bar{
    flex-grow: 1;
    background-color: blue;
    margin: 0 2px 0 2px;
    padding: 0 1px;

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
.bubbleCompare {
    background-color: orange;
}
.bubbleCompare2 {
    background-color: purple;
}
.insertionHighlight {
    background-color: white;
}
.selectionCompare {
    background-color:red;
}
.selectionCompare2 {
    background-color: green;
}
.selectionCompare3 {
    background-color: purple;
}
.selectionCompare4 {
    background-color: white;
}
</style>
