# **vue-data-pager**

### A batteries-included data paging solution for Vue.js 2+

The ability to page through data items is a familiar and expected feature in modern web apps. Unfortunately, implementing effective paging in a real application is anything but trivial. Most pagination controls require you to implement the math and logic for controlling the interaction between the pager component and your data view. Furthermore, providing options such as a way to choose the number of items displayed, usually requires adding an additional control and a fair amount of code to wire it up to the pagination control. 
  

The **vue-data-pager** deals with much of this complexity for you. The component is built to serve many typical use cases. It is simple to use, generally adding only a few lines of code to an existing page view, and handles most of the complex logic and wiring internally.
## [Demo](https://tomagnew.github.io/vue.js/vue-data-pager/# "see the vue-data-pager in action")


**Features:**  
* Handles many typical use cases with minimal code.  
* Wraps the established [vuejs-paginate](https://github.com/lokyoung/vuejs-paginate "vuejs-paginate") control.  
* Provides a built-in control for user selection of page size.  
* Allows for including multiple pagers in a single view.  
* Flexible styling with Bootstrap & Semantic defaults.  
  
**Tutorials**  
Watch a detailed YouTube tutorial on the **vue-data-pager** [here!](https://tomagnew.github.io/vue.js/vue-data-pager/# "see the vue-data-pager in action")

**Installation:**
```shell
npm install --save vue-data-pager
```
```javascript
import DataPager, { createPager } from 'vue-data-pager';

// DataPager represents the Vue component to be registered.  
// createPager is a helper function that creates a pager object for storing pager state.
```

The best way to integrate paging into an application using vue-data-pager, is to start out with a Vue component which can fetch data and display it without pagination. For example, displaying a list of contacts without paging might look like:

```html
<template>
    <div>
        <p v-for="contact in contacts">
            {{contact.name.first + ' ' + contact.name.last}}
        </p>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                contacts
            }
        },
        methods: {
            loadContacts() {
                // make ajax request to contacts endpoint...
                // then, assign response data to this.contacts
            },
        },
    }
</script>
```
Adding pagination using vue-data-pager might look like:
```html
<template>
    <div>
<data-pager v-bind="pager" @pageUpdated="updateContacts"  @pageArgsChanged="loadContacts"></data-pager>
        <p v-for="contact in pager.recordsPage">
            {{contact.name.first + ' ' + contact.name.last}}
        </p>
    </div>
</template>

<script>
    import DataPager, { createPager } from 'vue-data-pager';
        let pager = createPager();
    export default {
        components: {
            DataPager
        },
        data() {
            return {
                pager
            }
        },
        methods: {
            loadContacts({ pageNum, pageSize }) {  
                // make ajax request to contacts endpoint...
                // then, assign response data to pager.records
            },
            updateContacts(page){
                this.pager.recordsPage = page;
            }
        },
    }
</script>
```

If you wanted to be able to refer to the paged contacts as simply *'contacts'* in your data view component, you could simply set up a *computed* like:
```html
        <!-- template -->
        <p v-for="contact in contacts">
            {{contact.name.first + ' ' + contact.name.last}}
        </p>

        <script>
        export default {
            computed: {
                contacts(){
                    return this.pager.recordsPage
                }
        }
        </script>
```
In the previous example, the pager is operating in **standard mode** - the pager raises a "pageArgsChanged" event each time either a new page is selected, or the number of items displayed (page size) is changed. This event is pointed at a data api method which will load a new page of data.

However, what if you wanted to be able to load a block of say 500 records at a time, then have the ability to page through the records in-memory without making an api call to the server each time? For this, we can instruct the pager to operated in **block mode** like:

```javascript
    let pager = createPager({
        mode: 'block'
    });
```
Then just load the desired block of data from the server, from a text/JSON file, or whatever, then feed it to the pager like...
```javascript
loadContacts({pageNum:1, pageSize:500}) {
    // make ajax request to contacts endpoint...
    // then, assign response data to this.contacts
},
```
Remember that the page size (& page number) being used in the server query can be different than the pager's internal page number and page size being selected by the user. So, even though we're we're telling the server to retreive 500 records as a page size, the pager will display the selected page size (either default or custom) by the user.