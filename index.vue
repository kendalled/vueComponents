<template>
  <div id="root" class="text-gray-900 bg-gray-100 h-screen pt-3">
    <!-- Full width column -->
    <div class="flex">
      <div class="w-1/3 h-6 text-center content-center mb-4">
        <div class="h-4" />
        <p v-if="value == null || value.qty === 'How many emails?'" class="text-gray-600 pl-6">
          Select a quantity to begin.
        </p>
        <p v-else class="text-gray-800">
          {{ value.qty }} Emails Selected.
        </p>
      </div>
      <div class="w-1/3 h-6 text-center mb-4" />
      <div v-if="readyToSend" class="flex w-1/3 h-6 text-center mb-4 mr-16 justify-end">
        <p class="mr-3 text-gray-600">
          Click to send {{ doc.length }} emails.
        </p>
        <span class="border rounded-full border-grey flex items-center cursor-pointer w-12 bg-green-500 justify-end" @click="toggleReady">
          <span class="rounded-full border w-6 h-6 border-grey shadow-inner bg-white shadow" />
        </span>
      </div>
      <div v-else class="flex w-1/3 h-6 text-center mb-4 mr-16 justify-end">
        <p class="mr-3 text-gray-600">
          Are you ready to send?
        </p>
        <span class="border rounded-full border-grey flex items-center cursor-pointer w-12 justify-start" @click="toggleReady">
          <span class="rounded-full border w-6 h-6 border-grey shadow-inner bg-white shadow" />
        </span>
      </div>
    </div>
    <div class="flex px-4 mx-2 mb-2">
      <div class="flex-1 text-gray-700 text-center">
        <multiselect
          v-model="value"
          deselect-label="Can't remove this value"
          track-by="qty"
          label="qty"
          placeholder="How many emails?"
          :options="options"
          :searchable="false"
          :show-labels="false"
          :block-keys="['Tab', 'Enter']"
          :preselect-first="true"
          :allow-empty="false"
          @input="readFromFirestore"
        />
      </div>
      <div class="animated flex-1 w-1/3 h-16 text-center -mt-4">
        <img style="max-width: 200px; height: auto;" class="h-12 mx-auto" src="~/static/logo.png" />
        <p class="pt-3">Email database and mass mailer</p>
      </div>
      <div class="flex-1 text-gray-700 text-center">
        <SendEmails v-if="readyToSend" :audience="testData" :ids="deleteID" />
        <button v-else class="cursor-default w-5/6 bg-gray-500 text-white px-5 pt-2 pb-1 rounded-full focus:outline-none">
          Send Email Campaign
        </button>
      </div>
    </div>

    <div class="flex mb-4 justify-center text-center">
      <div class="flex justify-center items-center w-1/2 my-2 mx-6 h-64 pt-4 rounded bg-teal-400 h-12 text-white">
        <p class="text-xl">
          {{ doc.length }} Fresh Emails.
        </p>
      </div>
      <div class="flex justify-center items-center w-1/2 my-2 mx-6 h-64 pt-4 rounded bg-orange-400 h-12 text-white">
        <p class="text-xl">
          {{ waiting.length }} Emails are Cooling Down.
        </p>
      </div>
    </div>
    <p>
      {{ testData }}
    </p>
    <p>
      {{ deleteID }}
    </p>
  </div>
</template>
<script>
import Multiselect from 'vue-multiselect'
import { fireDb } from '~/plugins/firebase.js'
import SendEmails from '~/components/SendEmails'

export default {
  components: {
    SendEmails,
    Multiselect
  },
  head: {
    link: [
      { rel: 'stylesheet', href: 'https://fonts.googleapis.com/css?family=Manjari&display=swap' },
      { rel: 'stylesheet', href: 'https://unpkg.com/vue-multiselect@2.1.0/dist/vue-multiselect.min.css' }

    ]
  },
  data () {
    return {
      readyToSend: false,
      headings: [
        {
          title: 'Subscribed'
        },
        {
          title: 'Name'
        },
        {
          title: 'Email'
        },
        {
          title: 'Industry'
        },
        {
          title: 'City'
        },
        {
          title: 'State'
        },
        {
          title: 'contacted'
        }
      ],
      value: null,
      options: [
        { qty: 'How many emails?' },
        { qty: '500' },
        { qty: '1,000' },
        { qty: '5,000' },
        { qty: '10,000' },
        { qty: 'Custom', $isDisabled: true }
      ],
      writeSuccessful: false,
      readSuccessful: false,
      doc: [],
      waiting: [],
      ids: [],
      testData: [],
      deleteID: []
    }
  },
  async asyncData ({ app, params, error }) {
    const ref = fireDb.collection('Emails').where('substatus', '==', false)
    // remove line below
    const ref2 = fireDb.collection('Emails').where('email', '==', 'cori@lapelpinsandcoins.com')
    const ref3 = fireDb.collection('Emails').where('email', '==', 'snicolenyiri@aol.com')
    const ref4 = fireDb.collection('Emails').where('email', '==', 'kendallkj00@gmail.com')
    let data2
    let data3
    let testID
    try {
      data2 = []
      data3 = []
      testID = []
      await ref.get().then(function (querySnapshot) {
        querySnapshot.forEach(function (doc) {
        // doc.data() is never undefined for query doc snapshots
        // console.log(doc.id, ' => ', doc.data())
          data2.push(doc.data())
        })
      })
      await ref2.get().then(function (querySnapshot) {
        querySnapshot.forEach(function (doc) {
        // doc.data() is never undefined for query doc snapshots
        // console.log(doc.id, ' => ', doc.data())
          data3.push(doc.data())
          testID.push(doc.id)
        })
      })
      await ref3.get().then(function (querySnapshot) {
        querySnapshot.forEach(function (doc) {
        // doc.data() is never undefined for query doc snapshots
        // console.log(doc.id, ' => ', doc.data())
          data3.push(doc.data())
          testID.push(doc.id)
        })
      })
      await ref4.get().then(function (querySnapshot) {
        querySnapshot.forEach(function (doc) {
        // doc.data() is never undefined for query doc snapshots
        // console.log(doc.id, ' => ', doc.data())
          data3.push(doc.data())
          testID.push(doc.id)
        })
      })
    } catch (e) {
      // TODO: error handling
      console.error(e)
    }
    return {
      waiting: data2,
      testData: data3,
      deleteID: testID
    }
  },
  methods: {
    toggleReady () {
      if (this.doc.length === 0) {
        this.readyToSend = false
      } else {
        this.readyToSend = !this.readyToSend
      }
    },
    async readFromFirestore () {
      if (this.value.qty === 'How many emails?') {
        this.doc = []
        console.log('running')
      } else {
        let data
        let uniqueID
        try {
          data = []
          uniqueID = []
          await fireDb.collection('Emails').where('substatus', '==', true).limit(parseInt(this.value.qty.replace(/,/g, ''))).get().then(function (querySnapshot) {
            querySnapshot.forEach(function (doc) {
            // doc.data() is never undefined for query doc snapshots
            // console.log(doc.id, ' => ', doc.data())
              data.push(doc.data())
              uniqueID.push(doc.id)
            })
          })
        } catch (e) {
          // TODO: error handling
          console.error(e)
        }
        this.doc = data
        this.ids = uniqueID
      }
    },
    dynamicSort (property) {
      let sortOrder = 1
      if (property[0] === '-') {
        sortOrder = -1
        property = property.substr(1)
      }
      return function (a, b) {
        /* next line works with strings and numbers,
          * and you may want to customize it to your needs
          */
        const result = (a[property] < b[property]) ? -1 : (a[property] > b[property]) ? 1 : 0
        return result * sortOrder
      }
    }
  }
}
</script>

<style>
/* Sample `apply` at-rules with Tailwind CSS
.container {
  @apply min-h-screen flex justify-center items-center text-center mx-auto;
}
*/
#table {
  background: linear-gradient(180deg, rgba(222,248,249,.8) 65%, rgba(255,219,173,0.4) 100%);
}
#root {
  font-family: 'Manjari', sans-serif;
}
#yeet {
  font-family: 'Comic Sans MS';
}
.multiselect__single {
  line-height: 24px !important;
}
.animated:hover{
  cursor: pointer;
  animation: pulse linear 1.5s;
  animation-iteration-count: infinite;
  transform-origin: 50% 50%;
  -webkit-animation: pulse linear 1.5s;
  -webkit-animation-iteration-count: infinite;
  -webkit-transform-origin: 50% 50%;
  -moz-animation: pulse linear 1.5s;
  -moz-animation-iteration-count: infinite;
  -moz-transform-origin: 50% 50%;
  -o-animation: pulse linear 1.5s;
  -o-animation-iteration-count: infinite;
  -o-transform-origin: 50% 50%;
  -ms-animation: pulse linear 1.5s;
  -ms-animation-iteration-count: infinite;
  -ms-transform-origin: 50% 50%;
}

@keyframes pulse{
  0% {
    transform:  translate(0px,0px)  ;
  }
  10% {
    transform:  translate(-10px,0px)  ;
  }
  20% {
    transform:  translate(10px,0px)  ;
  }
  30% {
    transform:  translate(-10px,0px)  ;
  }
  40% {
    transform:  translate(10px,0px)  ;
  }
  50% {
    transform:  translate(-10px,0px)  ;
  }
  60% {
    transform:  translate(10px,0px)  ;
  }
  70% {
    transform:  translate(-10px,0px)  ;
  }
  80% {
    transform:  translate(10px,0px)  ;
  }
  90% {
    transform:  translate(-10px,0px)  ;
  }
  100% {
    transform:  translate(0px,0px)  ;
  }
}

@-moz-keyframes pulse{
  0% {
    -moz-transform:  translate(0px,0px)  ;
  }
  10% {
    -moz-transform:  translate(-10px,0px)  ;
  }
  20% {
    -moz-transform:  translate(10px,0px)  ;
  }
  30% {
    -moz-transform:  translate(-10px,0px)  ;
  }
  40% {
    -moz-transform:  translate(10px,0px)  ;
  }
  50% {
    -moz-transform:  translate(-10px,0px)  ;
  }
  60% {
    -moz-transform:  translate(10px,0px)  ;
  }
  70% {
    -moz-transform:  translate(-10px,0px)  ;
  }
  80% {
    -moz-transform:  translate(10px,0px)  ;
  }
  90% {
    -moz-transform:  translate(-10px,0px)  ;
  }
  100% {
    -moz-transform:  translate(0px,0px)  ;
  }
}

@-webkit-keyframes pulse {
  0% {
    -webkit-transform:  translate(0px,0px)  ;
  }
  10% {
    -webkit-transform:  translate(-10px,0px)  ;
  }
  20% {
    -webkit-transform:  translate(10px,0px)  ;
  }
  30% {
    -webkit-transform:  translate(-10px,0px)  ;
  }
  40% {
    -webkit-transform:  translate(10px,0px)  ;
  }
  50% {
    -webkit-transform:  translate(-10px,0px)  ;
  }
  60% {
    -webkit-transform:  translate(10px,0px)  ;
  }
  70% {
    -webkit-transform:  translate(-10px,0px)  ;
  }
  80% {
    -webkit-transform:  translate(10px,0px)  ;
  }
  90% {
    -webkit-transform:  translate(-10px,0px)  ;
  }
  100% {
    -webkit-transform:  translate(0px,0px)  ;
  }
}

@-o-keyframes pulse {
  0% {
    -o-transform:  translate(0px,0px)  ;
  }
  10% {
    -o-transform:  translate(-10px,0px)  ;
  }
  20% {
    -o-transform:  translate(10px,0px)  ;
  }
  30% {
    -o-transform:  translate(-10px,0px)  ;
  }
  40% {
    -o-transform:  translate(10px,0px)  ;
  }
  50% {
    -o-transform:  translate(-10px,0px)  ;
  }
  60% {
    -o-transform:  translate(10px,0px)  ;
  }
  70% {
    -o-transform:  translate(-10px,0px)  ;
  }
  80% {
    -o-transform:  translate(10px,0px)  ;
  }
  90% {
    -o-transform:  translate(-10px,0px)  ;
  }
  100% {
    -o-transform:  translate(0px,0px)  ;
  }
}

@-ms-keyframes pulse {
  0% {
    -ms-transform:  translate(0px,0px)  ;
  }
  10% {
    -ms-transform:  translate(-10px,0px)  ;
  }
  20% {
    -ms-transform:  translate(10px,0px)  ;
  }
  30% {
    -ms-transform:  translate(-10px,0px)  ;
  }
  40% {
    -ms-transform:  translate(10px,0px)  ;
  }
  50% {
    -ms-transform:  translate(-10px,0px)  ;
  }
  60% {
    -ms-transform:  translate(10px,0px)  ;
  }
  70% {
    -ms-transform:  translate(-10px,0px)  ;
  }
  80% {
    -ms-transform:  translate(10px,0px)  ;
  }
  90% {
    -ms-transform:  translate(-10px,0px)  ;
  }
  100% {
    -ms-transform:  translate(0px,0px)  ;
  }
}
</style>
