---
title: ""
url: https://supabase.com/docs/guides/getting-started/tutorials/with-ionic-vue
---

# 

Getting Started

#

Build a User Management App with Ionic Vue

* * *

This tutorial demonstrates how to build a basic user management app. The app authenticates and identifies the user, stores their profile information in the database, and allows the user to log in, update their profile details, and upload a profile photo. The app uses:

  * [Supabase Database](/docs/guides/database) \- a Postgres database for storing your user data and [Row Level Security](/docs/guides/auth#row-level-security) so data is protected and users can only access their own information.
  * [Supabase Auth](/docs/guides/auth) \- allow users to sign up and log in.
  * [Supabase Storage](/docs/guides/storage) \- allow users to upload a profile photo.


If you get stuck while working through this guide, refer to the [full example on GitHub](https://github.com/supabase/supabase/tree/master/examples/user-management/ionic-vue-user-management).

## Project setup#

Before you start building you need to set up the Database and API. You can do this by starting a new Project in Supabase and then creating a "schema" inside the database.

### Create a project#

  1. [Create a new project](/dashboard) in the Supabase Dashboard.
  2. Enter your project details.
  3. Wait for the new database to launch.


### Set up the database schema#

Now set up the database schema. You can use the "User Management Starter" quickstart in the SQL Editor, or you can copy/paste the SQL from below and run it.

DashboardSQL

  1. Go to the [SQL Editor](/dashboard/project/_/sql) page in the Dashboard.
  2. Click **User Management Starter** under the **Community > Quickstarts** tab.
  3. Click **Run**.


You can pull the database schema down to your local project by running the `db pull` command. Read the [local development docs](/docs/guides/cli/local-development#link-your-project) for detailed instructions.


    1

    supabase link --project-ref <project-id>

    2

    # You can get <project-id> from your project's dashboard URL: https://supabase.com/dashboard/project/<project-id>

    3

    supabase db pull

### Get API details#

Now that you've created some database tables, you are ready to insert data using the auto-generated API.

To do this, you need to get the Project URL and key from [the project **Connect** dialog](/dashboard/project/_?showConnect=true&connectTab=frameworks&framework=vuejs).

[Read the API keys docs](/docs/guides/getting-started/api-keys) for a full explanation of all key types and their uses.

##### Changes to API keys

Supabase is changing the way keys work to improve project security and developer experience. You can [read the full announcement on GitHub](https://github.com/orgs/supabase/discussions/29260).

The older `anon` and `service_role` keys will work until the end of 2026 but **we strongly encourage switching to and using** the new publishable (`sb_publishable_xxx`) and secret (`sb_secret_xxx`) keys now.

In most cases, you can get keys from [the Project's **Connect** dialog](/dashboard/project/_?showConnect=true&connectTab=frameworks&framework=vuejs), but if you want a specific key, you can find them in the [**Settings > API Keys**](/dashboard/project/_/settings/api-keys/) section of the Dashboard.

  * **For legacy keys** , copy the `anon` key for client-side operations and the `service_role` key for server-side operations from the **Legacy API Keys** tab.
  * **For new keys** , open the **API Keys** tab, if you don't have a publishable key already, click **Create new API Keys** , and copy the value from the **Publishable key** section.


## Building the app#

Start by building the Vue app from scratch.

### Initialize an Ionic Vue app#

Use the [Ionic CLI](https://ionicframework.com/docs/cli) to initialize an app called `supabase-ionic-vue`:


    1

    npm install -g @ionic/cli

    2

    ionic start supabase-ionic-vue blank --type vue

    3

    cd supabase-ionic-vue

Install the only additional dependency: [supabase-js](https://github.com/supabase/supabase-js)


    1

    npm install @supabase/supabase-js

Save the environment variables in a `.env` file, including the API URL and key that you copied earlier.

.env


    1

    VUE_APP_SUPABASE_URL=YOUR_SUPABASE_URL

    2

    VUE_APP_SUPABASE_KEY=YOUR_SUPABASE_KEY

With the API credentials in place, create a helper file to initialize the Supabase client. These variables will be exposed on the browser, and that's fine since Supabase enables [Row Level Security](/docs/guides/auth#row-level-security) on Databases by default.

###### src/supabase.ts


    1

    import { createClient } from '@supabase/supabase-js'

    2

    3

    const supabaseUrl = process.env.VUE_APP_SUPABASE_URL

    4

    const supabasePublishableKey = process.env.VUE_APP_SUPABASE_PUBLISHABLE_KEY

    5

    6

    if (!supabaseUrl) {

    7

      throw new Error(

    8

        'Environment variable VUE_APP_SUPABASE_URL is not set. Please define it before starting the application.'

    9

      )

    10

    }

    11

    12

    if (!supabasePublishableKey) {

    13

      throw new Error(

    14

        'Environment variable VUE_APP_SUPABASE_PUBLISHABLE_KEY is not set. Please define it before starting the application.'

    15

      )

    16

    }

    17

    18

    export const supabase = createClient(supabaseUrl, supabasePublishableKey)

[View source](https://github.com/supabase/supabase/blob/cc778cedbe02c32f70a8a6c22e02e66de1df1f7a/examples/user-management/ionic-vue-user-management/src/supabase.ts)

### Set up a login route#

Create a Vue component to manage logins and sign ups that uses Magic Links, so users can sign in with their email without using passwords.

###### src/views/Login.vue


    1

    <template>

    2

      <ion-page>

    3

        <ion-header>

    4

          <ion-toolbar>

    5

            <ion-title>Login</ion-title>

    6

          </ion-toolbar>

    7

        </ion-header>

    8

    9

        <ion-content>

    10

          <div class="ion-padding">

    11

            <h1>Supabase + Ionic Vue</h1>

    12

            <p>Sign in via magic link with your email below</p>

    13

          </div>

    14

          <ion-list inset="true">

    15

            <form @submit.prevent="handleLogin">

    16

              <ion-item>

    17

                <ion-input

    18

                  v-model="email"

    19

                  label="Email"

    20

                  label-placement="stacked"

    21

                  name="email"

    22

                  autocomplete="email"

    23

                  type="email"

    24

                ></ion-input>

    25

              </ion-item>

    26

              <div class="ion-text-center">

    27

                <ion-button type="submit" fill="clear">Login</ion-button>

    28

              </div>

    29

            </form>

    30

          </ion-list>

    31

          <p>{{ email }}</p>

    32

        </ion-content>

    33

      </ion-page>

    34

    </template>

    35

    36

    <script setup lang="ts">

    37

    import { supabase } from '../supabase';

    38

    import {

    39

      IonContent,

    40

      IonHeader,

    41

      IonPage,

    42

      IonTitle,

    43

      IonToolbar,

    44

      IonList,

    45

      IonItem,

    46

      IonInput,

    47

      IonButton,

    48

      toastController,

    49

      loadingController,

    50

    } from '@ionic/vue';

    51

    import { ref } from 'vue';

    52

    53

    const email = ref('');

    54

    55

    const handleLogin = async () => {

    56

      const loader = await loadingController.create({});

    57

      const toast = await toastController.create({ duration: 5000 });

    58

    59

      try {

    60

        await loader.present();

    61

        const { error } = await supabase.auth.signInWithOtp({ email: email.value });

    62

    63

        if (error) throw error;

    64

    65

        toast.message = 'Check your email for the login link!';

    66

        await toast.present();

    67

      } catch (error: any) {

    68

        toast.message = error.error_description || error.message;

    69

        await toast.present();

    70

      } finally {

    71

        await loader.dismiss();

    72

      }

    73

    };

    74

    </script>

[View source](https://github.com/supabase/supabase/blob/cc778cedbe02c32f70a8a6c22e02e66de1df1f7a/examples/user-management/ionic-vue-user-management/src/views/Login.vue)

### Account page#

After a user has signed in, let them edit their profile details and manage their account with a new component called `Account.vue`.

###### src/views/Account.vue


    1

    <template>

    2

      <ion-page>

    3

        <ion-header>

    4

          <ion-toolbar>

    5

            <ion-title>Account</ion-title>

    6

          </ion-toolbar>

    7

        </ion-header>

    8

    9

        <ion-content>

    10

    11

          // ...

    12

    13

          <form @submit.prevent="updateProfile">

    14

            <ion-item>

    15

              <ion-label>

    16

                <p>Email</p>

    17

                <p>{{ store.user?.email }}</p>

    18

              </ion-label>

    19

            </ion-item>

    20

    21

            <ion-item>

    22

              <ion-input

    23

                type="text"

    24

                name="username"

    25

                label="Name"

    26

                label-placement="stacked"

    27

                v-model="profile.username"

    28

              ></ion-input>

    29

            </ion-item>

    30

    31

            <ion-item>

    32

              <ion-input

    33

                type="url"

    34

                name="website"

    35

                label="Website"

    36

                label-placement="stacked"

    37

                v-model="profile.website"

    38

              ></ion-input>

    39

            </ion-item>

    40

            <div class="ion-text-center">

    41

              <ion-button fill="clear" type="submit">Update Profile</ion-button>

    42

            </div>

    43

          </form>

    44

    45

          <div class="ion-text-center">

    46

            <ion-button fill="clear" @click="signOut">Log Out</ion-button>

    47

          </div>

    48

        </ion-content>

    49

      </ion-page>

    50

    </template>

    51

    52

    <script setup lang="ts">

    53

    import { store } from '@/store';

    54

    import { supabase } from '@/supabase';

    55

    import {

    56

      IonContent,

    57

      IonHeader,

    58

      IonPage,

    59

      IonTitle,

    60

      IonToolbar,

    61

      toastController,

    62

      loadingController,

    63

      IonInput,

    64

      IonItem,

    65

      IonButton,

    66

      IonLabel,

    67

    } from '@ionic/vue';

    68

    import { onMounted, ref } from 'vue';

    69

    import { useRouter } from 'vue-router';

    70

    71

    // ...

    72

    73

    74

    const router = useRouter();

    75

    76

    const profile = ref({

    77

      username: '',

    78

      website: '',

    79

      avatar_url: '',

    80

    });

    81

    82

    async function getProfile() {

    83

      const loader = await loadingController.create({});

    84

      const toast = await toastController.create({ duration: 5000 });

    85

      await loader.present();

    86

      try {

    87

        const { data: { claims } } = await supabase.auth.getClaims();

    88

        if (!claims) throw new Error('No user logged in');

    89

    90

        const { data, error, status } = await supabase

    91

          .from('profiles')

    92

          .select(`username, website, avatar_url`)

    93

          .eq('id', claims.sub)

    94

          .single();

    95

    96

        if (error && status !== 406) throw error;

    97

    98

        if (data) {

    99

          profile.value = {

    100

            username: data.username,

    101

            website: data.website,

    102

            avatar_url: data.avatar_url,

    103

          };

    104

        }

    105

      } catch (error: any) {

    106

        toast.message = error.message;

    107

        await toast.present();

    108

      } finally {

    109

        await loader.dismiss();

    110

      }

    111

    }

    112

    113

    const updateProfile = async () => {

    114

      const loader = await loadingController.create({});

    115

      const toast = await toastController.create({ duration: 5000 });

    116

      try {

    117

        await loader.present();

    118

        const { data: { claims } } = await supabase.auth.getClaims();

    119

        if (!claims) throw new Error('No user logged in');

    120

    121

        const updates = {

    122

          id: claims.sub,

    123

          ...profile.value,

    124

          updated_at: new Date(),

    125

        };

    126

    127

        const { error } = await supabase.from('profiles').upsert(updates);

    128

    129

        if (error) throw error;

    130

      } catch (error: any) {

    131

        toast.message = error.message;

    132

        await toast.present();

    133

      } finally {

    134

        await loader.dismiss();

    135

      }

    136

    };

    137

    138

    async function signOut() {

    139

      const loader = await loadingController.create({});

    140

      const toast = await toastController.create({ duration: 5000 });

    141

      await loader.present();

    142

      try {

    143

        const { error } = await supabase.auth.signOut();

    144

        if (error) throw error;

    145

        await router.push('/');

    146

      } catch (error: any) {

    147

        toast.message = error.message;

    148

        await toast.present();

    149

      } finally {

    150

        await loader.dismiss();

    151

      }

    152

    }

    153

    154

    onMounted(() => {

    155

      getProfile();

    156

    });

    157

    </script>

[View source](https://github.com/supabase/supabase/blob/cc778cedbe02c32f70a8a6c22e02e66de1df1f7a/examples/user-management/ionic-vue-user-management/src/views/Account.vue)

### Launch!#

With all the components in place, update `App.vue` and the app routes:

###### src/router/index.ts


    1

    import { createRouter, createWebHistory } from '@ionic/vue-router'

    2

    import { RouteRecordRaw } from 'vue-router'

    3

    import LoginPage from '../views/Login.vue'

    4

    import AccountPage from '../views/Account.vue'

    5

    const routes: Array<RouteRecordRaw> = [

    6

      {

    7

        path: '/',

    8

        name: 'Login',

    9

        component: LoginPage,

    10

      },

    11

      {

    12

        path: '/account',

    13

        name: 'Account',

    14

        component: AccountPage,

    15

      },

    16

    ]

    17

    18

    const router = createRouter({

    19

      history: createWebHistory(process.env.BASE_URL),

    20

      routes,

    21

    })

    22

    23

    export default router

[View source](https://github.com/supabase/supabase/blob/cc778cedbe02c32f70a8a6c22e02e66de1df1f7a/examples/user-management/ionic-vue-user-management/src/router/index.ts)

###### src/App.vue


    1

    <template>

    2

      <ion-app>

    3

        <ion-router-outlet />

    4

      </ion-app>

    5

    </template>

    6

    7

    <script setup lang="ts">

    8

    import { IonApp, IonRouterOutlet, useIonRouter } from '@ionic/vue';

    9

    import { onUnmounted } from 'vue';

    10

    import { store } from './store';

    11

    import { supabase } from './supabase';

    12

    13

    const router = useIonRouter();

    14

    15

    supabase.auth.getClaims().then(({ data: { claims } }) => {

    16

      store.user = claims;

    17

    });

    18

    19

    const {

    20

      data: { subscription },

    21

    } = supabase.auth.onAuthStateChange((_event, session) => {

    22

      store.user = session?.user ?? null;

    23

      if (session?.user) {

    24

        router.replace('/account');

    25

      } else {

    26

        router.replace('/');

    27

      }

    28

    });

    29

    30

    onUnmounted(() => {

    31

      subscription.unsubscribe();

    32

    });

    33

    </script>

[View source](https://github.com/supabase/supabase/blob/cc778cedbe02c32f70a8a6c22e02e66de1df1f7a/examples/user-management/ionic-vue-user-management/src/App.vue)

Once that's done, run this in a terminal window:


    1

    ionic serve

And then open the browser to [localhost:8100](http://localhost:8100) and you should see the completed app.

## Bonus: Profile photos#

Every Supabase project is configured with [Storage](/docs/guides/storage) for managing large files like photos and videos.

### Create an upload widget#

First install two packages to interact with the user's camera.


    1

    npm install @ionic/pwa-elements @capacitor/camera

[Capacitor](https://capacitorjs.com) is a cross-platform native runtime from Ionic that enables you to deploy web apps to app stores and provides access to native device API.

Ionic PWA elements is a companion package that polyfills certain browser APIs that provide no user interface with custom Ionic UI.

With those packages installed, update `main.ts` to include an additional bootstrapping call for the Ionic PWA Elements.

###### src/main.ts


    1

    import { createApp } from 'vue'

    2

    import App from './App.vue'

    3

    import router from './router'

    4

    5

    import { IonicVue } from '@ionic/vue'

    6

    /* Core CSS required for Ionic components to work properly */

    7

    import '@ionic/vue/css/ionic.bundle.css'

    8

    9

    /* Theme variables */

    10

    import './theme/variables.css'

    11

    12

    import { defineCustomElements } from '@ionic/pwa-elements/loader'

    13

    defineCustomElements(window)

    14

    const app = createApp(App).use(IonicVue).use(router)

    15

    16

    router.isReady().then(() => {

    17

      app.mount('#app')

    18

    })

[View source](https://github.com/supabase/supabase/blob/cc778cedbe02c32f70a8a6c22e02e66de1df1f7a/examples/user-management/ionic-vue-user-management/src/main.ts)

Then create an `AvatarComponent`.

###### src/components/Avatar.vue


    1

    <template>

    2

      <div class="avatar">

    3

        <div class="avatar_wrapper" @click="uploadAvatar">

    4

          <img v-if="avatarUrl" :src="avatarUrl" />

    5

          <ion-icon v-else :icon="person" class="no-avatar"></ion-icon>

    6

        </div>

    7

      </div>

    8

    </template>

    9

    10

    <script setup lang="ts">

    11

    import { ref, toRef, watch } from 'vue';

    12

    import { supabase } from '../supabase';

    13

    import { Camera, CameraResultType } from '@capacitor/camera';

    14

    import { IonIcon } from '@ionic/vue';

    15

    import { person } from 'ionicons/icons';

    16

    17

    const props = defineProps<{ path?: string }>();

    18

    const emit = defineEmits<{

    19

      upload: [];

    20

      'update:path': [value: string];

    21

    }>();

    22

    23

    const path = toRef(props, 'path');

    24

    const avatarUrl = ref('');

    25

    26

    const downloadImage = async () => {

    27

      try {

    28

        const { data, error } = await supabase.storage

    29

          .from('avatars')

    30

          .download(path.value!);

    31

        if (error) throw error;

    32

        avatarUrl.value = URL.createObjectURL(data!);

    33

      } catch (error: any) {

    34

        console.error('Error downloading image: ', error.message);

    35

      }

    36

    };

    37

    38

    const uploadAvatar = async () => {

    39

      try {

    40

        const photo = await Camera.getPhoto({

    41

          resultType: CameraResultType.DataUrl,

    42

        });

    43

    44

        if (photo.dataUrl) {

    45

          const file = await fetch(photo.dataUrl)

    46

            .then((res) => res.blob())

    47

            .then(

    48

              (blob) =>

    49

                new File([blob], 'my-file', { type: `image/${photo.format}` })

    50

            );

    51

    52

          const fileName = `${Math.random()}-${new Date().getTime()}.${

    53

            photo.format

    54

          }`;

    55

          const { error: uploadError } = await supabase.storage

    56

            .from('avatars')

    57

            .upload(fileName, file);

    58

          if (uploadError) {

    59

            throw uploadError;

    60

          }

    61

          emit('update:path', fileName);

    62

          emit('upload');

    63

        }

    64

      } catch (error) {

    65

        console.log(error);

    66

      }

    67

    };

    68

    69

    watch(path, () => {

    70

      if (path.value) downloadImage();

    71

    });

    72

    </script>

    73

    74

    <style>

    75

    .avatar {

    76

      display: block;

    77

      margin: auto;

    78

      min-height: 150px;

    79

    }

    80

    .avatar .avatar_wrapper {

    81

      margin: 16px auto 16px;

    82

      border-radius: 50%;

    83

      overflow: hidden;

    84

      height: 150px;

    85

      aspect-ratio: 1;

    86

      background: var(--ion-color-step-50);

    87

      border: thick solid var(--ion-color-step-200);

    88

    }

    89

    .avatar .avatar_wrapper:hover {

    90

      cursor: pointer;

    91

    }

    92

    .avatar .avatar_wrapper ion-icon.no-avatar {

    93

      width: 100%;

    94

      height: 115%;

    95

    }

    96

    .avatar img {

    97

      display: block;

    98

      object-fit: cover;

    99

      width: 100%;

    100

      height: 100%;

    101

    }

    102

    </style>

[View source](https://github.com/supabase/supabase/blob/cc778cedbe02c32f70a8a6c22e02e66de1df1f7a/examples/user-management/ionic-vue-user-management/src/components/Avatar.vue)

### Add the new widget#

And then add the widget to the Account page:

###### src/views/Account.vue


    1

    <template>

    2

      <ion-page>

    3

        <ion-header>

    4

          <ion-toolbar>

    5

            <ion-title>Account</ion-title>

    6

          </ion-toolbar>

    7

        </ion-header>

    8

    9

        <ion-content>

    10

          <avatar v-model:path="profile.avatar_url" @upload="updateProfile"></avatar>

    11

          <form @submit.prevent="updateProfile">

    12

            <ion-item>

    13

              <ion-label>

    14

                <p>Email</p>

    15

                <p>{{ store.user?.email }}</p>

    16

              </ion-label>

    17

            </ion-item>

    18

    19

            <ion-item>

    20

              <ion-input

    21

                type="text"

    22

                name="username"

    23

                label="Name"

    24

                label-placement="stacked"

    25

                v-model="profile.username"

    26

              ></ion-input>

    27

            </ion-item>

    28

    29

            <ion-item>

    30

              <ion-input

    31

                type="url"

    32

                name="website"

    33

                label="Website"

    34

                label-placement="stacked"

    35

                v-model="profile.website"

    36

              ></ion-input>

    37

            </ion-item>

    38

            <div class="ion-text-center">

    39

              <ion-button fill="clear" type="submit">Update Profile</ion-button>

    40

            </div>

    41

          </form>

    42

    43

          <div class="ion-text-center">

    44

            <ion-button fill="clear" @click="signOut">Log Out</ion-button>

    45

          </div>

    46

        </ion-content>

    47

      </ion-page>

    48

    </template>

    49

    50

    <script setup lang="ts">

    51

    import { store } from '@/store';

    52

    import { supabase } from '@/supabase';

    53

    import {

    54

      IonContent,

    55

      IonHeader,

    56

      IonPage,

    57

      IonTitle,

    58

      IonToolbar,

    59

      toastController,

    60

      loadingController,

    61

      IonInput,

    62

      IonItem,

    63

      IonButton,

    64

      IonLabel,

    65

    } from '@ionic/vue';

    66

    import { onMounted, ref } from 'vue';

    67

    import { useRouter } from 'vue-router';

    68

    import Avatar from '../components/Avatar.vue';

    69

    70

    const router = useRouter();

    71

    72

    const profile = ref({

    73

      username: '',

    74

      website: '',

    75

      avatar_url: '',

    76

    });

    77

    78

    async function getProfile() {

    79

      const loader = await loadingController.create({});

    80

      const toast = await toastController.create({ duration: 5000 });

    81

      await loader.present();

    82

      try {

    83

        const { data: { claims } } = await supabase.auth.getClaims();

    84

        if (!claims) throw new Error('No user logged in');

    85

    86

        const { data, error, status } = await supabase

    87

          .from('profiles')

    88

          .select(`username, website, avatar_url`)

    89

          .eq('id', claims.sub)

    90

          .single();

    91

    92

        if (error && status !== 406) throw error;

    93

    94

        if (data) {

    95

          profile.value = {

    96

            username: data.username,

    97

            website: data.website,

    98

            avatar_url: data.avatar_url,

    99

          };

    100

        }

    101

      } catch (error: any) {

    102

        toast.message = error.message;

    103

        await toast.present();

    104

      } finally {

    105

        await loader.dismiss();

    106

      }

    107

    }

    108

    109

    const updateProfile = async () => {

    110

      const loader = await loadingController.create({});

    111

      const toast = await toastController.create({ duration: 5000 });

    112

      try {

    113

        await loader.present();

    114

        const { data: { claims } } = await supabase.auth.getClaims();

    115

        if (!claims) throw new Error('No user logged in');

    116

    117

        const updates = {

    118

          id: claims.sub,

    119

          ...profile.value,

    120

          updated_at: new Date(),

    121

        };

    122

    123

        const { error } = await supabase.from('profiles').upsert(updates);

    124

    125

        if (error) throw error;

    126

      } catch (error: any) {

    127

        toast.message = error.message;

    128

        await toast.present();

    129

      } finally {

    130

        await loader.dismiss();

    131

      }

    132

    };

    133

    134

    async function signOut() {

    135

      const loader = await loadingController.create({});

    136

      const toast = await toastController.create({ duration: 5000 });

    137

      await loader.present();

    138

      try {

    139

        const { error } = await supabase.auth.signOut();

    140

        if (error) throw error;

    141

        await router.push('/');

    142

      } catch (error: any) {

    143

        toast.message = error.message;

    144

        await toast.present();

    145

      } finally {

    146

        await loader.dismiss();

    147

      }

    148

    }

    149

    150

    onMounted(() => {

    151

      getProfile();

    152

    });

    153

    </script>

[View source](https://github.com/supabase/supabase/blob/cc778cedbe02c32f70a8a6c22e02e66de1df1f7a/examples/user-management/ionic-vue-user-management/src/views/Account.vue)

At this stage you have a fully functional application!

### Is this helpful?

No Yes

### AI Tools

Copy as Markdown[Ask ChatGPT](https://chatgpt.com/?hint=search&q=Read from https://supabase.com/docs/guides/getting-started/tutorials/with-ionic-vue so I can ask questions about its contents)[Ask Claude](https://claude.ai/new?q=Read from https://supabase.com/docs/guides/getting-started/tutorials/with-ionic-vue so I can ask questions about its contents)