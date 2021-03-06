# Getting Started - Hands On

> A example project using vue-crud-x component

## Learning To Use Or Maintaining Component (Github)

### Learning

#### clone the repository and go to the repository example
    git clone https://github.com/ais-one/vue-crud-x.git
    cd vue-crud-x/example

#### install dependencies
    npm install
    # delete package-lock.json if you face problems

#### create cfg.json file & put in your credentials
    touch cfg.json
    vi cfg.json

    {
      "firebaseCfg": {
        "apiKey": "",
        "authDomain": "",
        "databaseURL": "",
        "projectId": "",
        "storageBucket": "",
        "messagingSenderId": ""
      },
      "recaptchaSiteKey": ""  
    }

#### run the app (vue-cli 3)
    npm run serve

### Maintaining - Build NPM Package

#### go to repo root directory and build for production with minification
    cd [path-to]/vue-crud-x
    npm run build

#### Either upload as published package (only for repo owner)
    npm publish

#### local package vue-crud-x
    npm pack
    # A local npm package will be created (e.g. vue-crud-x-?.?.?.tgz file)
    # If you want to install without saving to package.json, npm i --no-save vue-crud-x-?.?.?.tgz

## General Usage

### Option 1 use NPM package

Install it as in NPM package and import it
    npm i vue-crud-x

### Option 2 use from source file

Just copy the VueCrudX.vue file into your project and include it as a component

---

# A Deeper Dive Into The Example

## VueCrudX Important Notes

* primary key field name must be id - for now
* the example uses firestore, please signup for firebase and create user on firebase to access
* please see Schema section below for simple schema description, indexing, etc.
 - party collection (e.g. a party to a lawsuit)
 - note collection (case notes on each party)
* there is a user state which is passed into payload
 - you pass in email or id, to be used in createdBy field, to indicate who created the document/record (optional)
 - you pass in rules object to determine whether to show add, edit, delete button, etc. 
* events emitted: form-close, selected
    # see using in VueCrudX.vue, you can populate the user state or leave it undefined
    async createRecord ({ commit, getters, dispatch }, payload) {
      payload.user = this.getters.user
      let res = await getters.crudOps.create(payload)
      return res
    }

    # sample user JSON - obtained from backend, decoded from JWT or some other means
    {
      id: '1726388390',
      email: 'abc@mail.com',
      rules: { // if this is not present, all front end permissions are enabled (but backend can still disallow)
        '*': ['*'], // evereything thing allowed also in this setting
        'storename1': ['*'] // all operations available for certain storename (each crud component has a unique storename)
        '*': ['create,update,delete'] // all operations available for all stores also
        'storename2': ['create', 'update'] // only create and update allowed for storename2

      }
    }


## Folders & Files to take note of

Folders & Files providing examples of how the user can customize this vue-crud-x component

### Mulitple CRUD Component on a page [created in the vue file]

**/src/components/MultiPageCrud**

Two components are shown on a single page (you can also add other components on the page, e.g. maps, charts, other vue components)

* Clicking on a party in the party table on the left will select all notes related to that party and display the list of notes on the right
* On the party table, the icons on the left are to open update dialog or to delete the party
* On the party notes table, just click row to open a dialog which you can use to edit or delete the row
  - there is no "go back" button to return it to parent component (it is set to hidden for this case, as to is not needed)

**Party Component**

* party.js
* PartyForm.vue - customized crud form
* PartyFilter.vue - customize search form

**Party Notes Component**

* party-notes.js
* partyNotesForm.vue - customized crud form
* PartyNotesFilter.vue - customize search form

### Single CRUD Component on a page [created in router]

**/src/components/Crud**

The files below shows the usual usage for a single crud component which is able to open child crud

* notes.js
 - crudOps object is where you can customise your crud operations, connection to DB, API, etc. The methods are find, findOne, create, update, delete, leave create, update, or delete null to disable their operation
 - exportOps object is where you can pass the function for exporting data from the table
 - crudTable object is where you specify the columns and how they are formatted
 - crudFilter object is where you specify the data involved in filtering, and the filename of the Vue file you created
 - crudForm object is where you specify what are the data the form operates on, and the filename of the Vue file you created
* NotesForm.vue
 - you customise how the form looks like here, take note of the button that leads you to next nested level of the crud
* no filter file, autogenerated from filters specified in notes.js

The files below are to show that duplicate components can be created with different Form & Filter look and feel

* notes2.js
* NotesForm2.vue
* no filter file, autogenerated from filters specified in notes2.js


The files below are for the Party information (a party to a lawsuit)

* party.js
* PartyForm.vue
* Filter.vue (can be removed and use autogenerated filter instead)

The files below are nested crud, with records retrieved based on a parent Key (can be primary key or unique key), further filtered by search conditions

* party-notes.js
* partyNotesForm.vue
* no filter file, autogenerated from filters specified in party-notes.js

The files below show inline edit example

* party-inline.js

### Things to take note of in /src/router/index.js

Look at the routes and how the props are passed in

### Things to take note of in /src/App.vue

Look at menuItems and see the how the menu items and link to are done

### payload passed into methods created in crudOps

    -- user & token object get from vuex --
    export {user, filterData}
    find {user, pagination, parentId, filterData, doPage}
    findOne {user, id}
    update {user, record}
    create {user, record, parentId}
    delete {user, record}

---

## Schema

### indexes
note - approveStatus a, datetime d
note - approveStatus a, datetime a
note - datetime d, approveStatus a
note - party a, datetime a

### collections

    party {
      id: String
      name: String
      remarks: String
      status: String (select)
      languages: Array of String
      photo: String
    }

    note {
      id: String
      party: String (select)
      partyId: String
      type: String
      value: String
      approveStatus: String (select)
      Approver: String
      datetime: String
    }

### relation

    1 party :-> N notes

## Notes Creation Of vue-cli3 project From Scratch

vue create [project name]
Manually select features
Babel, Linter, CSS Preporcessors
Stylus
ESLint + Standard config
Lint on save
dedicated config file (for babel, postcss, eslint, etc)
Save this as a preset? N

---

# Firebase

## Hosting To Firebase

https://firebase.google.com/docs/hosting/quickstart


1. Install Firebase

```
npm install -g firebase-tools (first time)
firebase login (first time)
cd to project folder
firebase init (first time)
add files
```

2. Build the production version first

```
npm run build
# optional: build for production and view the bundle analyzer report
# npm run build --report
```

3. Deploy to Firebase

```
firebase deploy --only hosting
firebase logout
```

## Storage Rules

```
service firebase.storage {
  match /b/{bucket}/o {
    match /{allPaths=**} {
      allow read;
      allow write: if request.auth != null;
    }
  }
}
```

## Firestore NoSQL Rules & Indexes

```
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      // allow read, write;
      allow read, write: if request.auth.uid != null;
    }
  }
}
```

- note: party ASC datetime ASC
- note: approveStatus ASC datetime DESC
- note approveStatus ASC datetime ASC
- note datetime DESC approveStatus ASC


# Other Notes (Related To VueJS only)

## FILTERS Usage...

    import DateFilter from './filters/date'

    // Global - filter
    Vue.filter('date', DateFilter)

    export default (value) => {
      const date = new Date(value)
      return date.toLocaleDateString(['sq-AL'], {year: 'numeric', month: '2-digit', day: '2-digit'})
    }


    // Global - mixin
    // https://stackoverflow.com/questions/42613061/vue-js-making-helper-functions-globally-available-to-single-file-components
    Vue.mixin({
      methods: {
        mixinMethodOne: function (arg0, arg1) { // available globally using this.mixinMethodOne
          // code goes here
        }
      }
    })


## Debounce Example

    var watchExampleVM = new Vue({
      el: '#watch-example',
      data: {
        question: '',
        answer: 'I cannot give you an answer until you ask a question!'
      },
      watch: {
        // whenever question changes, this function will run
        question: function (newQuestion) {
          this.answer = 'Waiting for you to stop typing...'
          this.getAnswer()
        }
      },
      methods: {
        // _.debounce is a function provided by lodash to limit how
        // often a particularly expensive operation can be run.
        // In this case, we want to limit how often we access
        // yesno.wtf/api, waiting until the user has completely
        // finished typing before making the ajax request. To learn
        // more about the _.debounce function (and its cousin
        // _.throttle), visit: https://lodash.com/docs#debounce
        getAnswer: _.debounce(
          function () {
            if (this.question.indexOf('?') === -1) {
              this.answer = 'Questions usually contain a question mark. ;-)'
              return
            }
            this.answer = 'Thinking...'
            var vm = this
            axios.get('https://yesno.wtf/api')
              .then(function (response) {
                vm.answer = _.capitalize(response.data.answer)
              })
              .catch(function (error) {
                vm.answer = 'Error! Could not reach the API. ' + error
              })
          },
          // This is the number of milliseconds we wait for the
          // user to stop typing.
          500
        )
      }
    })

## Others

v-model.lazy.trim.number (v-model modifiers)

computed: for non-async data
 - get, set (used for non-referenced data)
watch: for async data
 - newval, oldval

v-if (create / remove the block, can use v-else)
v-show (still renders block, but hide it)

: v-bind classes, styles
@ v-on submit, keyup

v-for="(item,index)" in items :key="item.id"
or (value, key, index) if looping object

ref is non-reactive

vue lifecycle avoid beforeUpdate or update, use computed & watchers first

https://firebase.google.com/docs/auth/web/google-signin

---

# Other notes

0. look into id => name?
1. add Field as hidden on table list
2. parentId for creating new record - done

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).

- ! config info for dev and build
- fix "global rules"
- close filter/search component programatically
- async select dropdown
- multiple select
- recaptcha (https://github.com/DanSnow/vue-recaptcha)

- https://stackoverflow.com/questions/44324454/vuejs-accessing-store-data-inside-mounted
- https://vuex.vuejs.org/en/forms.html
- https://forum.vuejs.org/t/dont-understand-how-to-use-mapstate-from-the-docs/14454/12
- https://vuex.vuejs.org

- https://medium.com/@paul.allies/stateless-auth-with-express-passport-jwt-7a55ffae0a5c
- https://medium.com/front-end-hacking/learn-using-jwt-with-passport-authentication-9761539c4314
- https://bigcodenerd.org/enforce-cloud-firestore-unique-field-values/
- https://medium.com/@jqualls/firebase-firestore-unique-constraints-d0673b7a4952
- https://scotch.io/tutorials/vue-authentication-and-route-handling-using-vue-router
- https://scotch.io/tutorials/managing-user-permissions-in-vue-using-casl
- https://medium.com/@jqualls/firebase-firestore-unique-constraints-d0673b7a4952
- https://timonweb.com/posts/how-to-enable-es6-imports-in-nodejs/
- https://medium.com/@pariola/using-es6-in-cloud-functions-387af36bcee3
- https://codeburst.io/es6-in-cloud-functions-for-firebase-959b35e31cb0
- https://github.com/jthegedus/firebase-gcp-examples/tree/master/firebase-functions-es6-babel
- https://itnext.io/csv-or-json-export-with-firebase-functions-fa4a4b1416aa
