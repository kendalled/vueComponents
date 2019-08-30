<template>
  <div class="text-gray-900 bg-gray-200 h-full pt-1">
    <div class="flex h-30 px-4 py-2 mx-2 mb-2">
      <div class="flex-1 text-gray-700 text-center">
        <NavActions />
      </div>
      <div class="flex-1 text-gray-700 text-center">
        <h1 class="text-3xl text-gray-800 w-full h-12 text-center">
          Email Boi
        </h1>
        <p>Email database and mass mailer.</p>
      </div>
      <div class="flex-1 text-gray-700 text-center">
        <SearchBar />
      </div>
    </div>

    <div class="px-8 py-4 flex justify-center">
      <!-- changed from bg-white to css -->
      <table id="table" class="w-full text-md shadow-md rounded mb-4">
        <thead class="border-b">
          <th class="bg-teal-500 rounded-tl" />
          <th v-for="(item, index) of headings" :key="index" class="text-left text-white p-3 px-5 bg-teal-500">
            <p class="cursor-pointer">
              {{ item.title }}
            </p>
          </th>
          <th class="text-left p-3 px-5 bg-teal-500 rounded-tr text-white">
            <p class="cursor-pointer">
              State
            </p>
          </th>
        </thead>
        <tbody>
          <!-- <tr>
            <th />
            <th v-for="(item, index) of headings" :key="index" class="text-left p-3 px-5">
              <p class="cursor-pointer">
                {{ item.title }}
              </p>
            </th>
          </tr> -->
          <tr class="border-b hover:text-teal-600 bg-white">
            <td class="p-3 px-5">
              <input type="checkbox">
            </td>
            <td class="p-3 px-5">
              <p>{{ name1 }}</p>
            </td>
            <td class="p-3 px-5">
              <p>{{ email1 }}</p>
            </td>
            <td class="p-3 px-5">
              <span v-if="!writeSuccessful" class="flex rounded-full bg-green-300 uppercase px-2 py-1 text-xs text-green-800 justify-center font-bold mr-3">{{ substatus1 }}</span>
              <span v-else class="flex rounded-full bg-red-300 uppercase px-2 py-1 text-xs text-red-800 justify-center font-bold mr-3">{{ substatus1 }}</span>
            </td>
            <td class="p-3 px-5">
              <p>{{ industry1 }}</p>
            </td>
            <td class="p-3 px-5">
              <p>{{ city1 }}</p>
            </td>
            <td class="p-3 px-5">
              <p>{{ state1 }}</p>
            </td>
          </tr>

          <tr class="border-b hover:text-teal-600">
            <td class="p-3 px-5">
              <input type="checkbox">
            </td>
            <td class="p-3 px-5">
              <p>{{ text4 }}</p>
            </td>
            <td class="p-3 px-5">
              <p>{{ text3 }}</p>
            </td>
            <td class="p-3 px-5">
              <span v-if="substatus2" class="flex rounded-full bg-green-300 uppercase px-2 py-1 text-xs text-green-800 justify-center font-bold mr-3">{{ substatus2 }}</span>
              <span v-else class="flex rounded-full bg-red-300 uppercase px-2 py-1 text-xs text-red-800 justify-center font-bold mr-3">{{ substatus2 }}</span>
            </td>
            <td class="p-3 px-5">
              <p>Technology</p>
            </td>
            <td class="p-3 px-5">
              <p>Orlando</p>
            </td>
            <td class="p-3 px-5">
              <p>Florida</p>
            </td>
          </tr>

          <!-- for item in contacts -->
          <tr v-for="n in 23" :key="n" class="border-b hover:text-teal-600 odd:bg-white last:border-0">
            <td class="p-3 px-5">
              <input type="checkbox">
            </td>
            <td class="p-3 px-5">
              <p>user.name</p>
            </td>
            <td class="p-3 px-5">
              <p>user.email</p>
            </td>
            <td class="p-3 px-5">
              <p>True</p>
            </td>
            <td class="p-3 px-5">
              <p>Technology</p>
            </td>
            <td class="p-3 px-5">
              <p>Orlando</p>
            </td>
            <td class="p-3 px-5">
              <p>Florida</p>
            </td>
            <!-- <td class="p-3 px-5 flex justify-end">
              <button type="button" class="mr-3 text-sm bg-blue-500 hover:bg-blue-700 text-white py-1 px-2 rounded focus:outline-none focus:shadow-outline">
                Save
              </button><button type="button" class="text-sm bg-red-500 hover:bg-red-700 text-white py-1 px-2 rounded focus:outline-none focus:shadow-outline">
                Delete
              </button>
            </td> -->
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>
<script>
import { fireDb } from '~/plugins/firebase.js'
import NavActions from '~/components/NavActions'
import SearchBar from '~/components/SearchBar'
export default {
  components: {
    NavActions,
    SearchBar
  },
  data () {
    return {

      headings: [
        {
          title: 'Name'
        },
        {
          title: 'Email'
        },
        {
          title: 'Subscribed'
        },
        {
          title: 'Industry'
        },
        {
          title: 'City'
        }
      ],
      writeSuccessful: false,
      readSuccessful: false,
      email1: '',
      name1: '',
      substatus1: true,
      industry1: '',
      city1: '',
      state1: '',
      text3: '',
      text4: '',
      substatus2: true
    }
  },
  async asyncData ({ app, params, error }) {
    const ref = fireDb.collection('Emails').doc('TestEmail')
    const ref2 = fireDb.collection('Emails').doc('TestEmail2')
    let snap
    let snap2
    try {
      snap = await ref.get()
      snap2 = await ref2.get()
    } catch (e) {
      // TODO: error handling
      console.error(e)
    }
    return {
      email1: snap.data().email,
      name1: snap.data().business,
      substatus1: snap.data().substatus,
      industry1: snap.data().industry,
      city1: snap.data().city,
      state1: snap.data().state,
      text3: snap2.data().email,
      text4: snap2.data().business,
      substatus2: snap2.data().substatus
    }
  },
  methods: {
    async writeToFirestore () {
      const ref = fireDb.collection('Emails').doc('test')
      const document = {
        text: 'This is a test message.'
      }
      try {
        await ref.set(document)
      } catch (e) {
        // TODO: error handling
        console.error(e)
      }
      this.writeSuccessful = true
    },
    async readFromFirestore () {
      const ref = fireDb.collection('Emails').doc('TestEmail')
      let snap
      try {
        snap = await ref.get()
      } catch (e) {
        // TODO: error handling
        console.error(e)
      }
      this.text = snap.data().email
      this.readSuccessful = true
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
</style>
