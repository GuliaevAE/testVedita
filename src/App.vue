<template>
  <v-app>
    <v-main class="d-flex flex-row mx-auto">
      <v-card
        class="d-flex flex-column justify-space-between align-center px-md-8 mt-8 mx-4"
        max-width="1200"
      >
        <v-container white fluid>
          <v-row>
            <v-col class="col-9 px-0 py-0 header">
              <v-card-title>
                <span>Список</span>
                <span>пользователей</span>
              </v-card-title>
              <v-card-subtitle v-if=" !countOfUsers.length ">Не добавлены</v-card-subtitle>
              <v-card-subtitle v-if="availableToRedacting && countOfUsers.length">Заполните данные</v-card-subtitle>
              <v-card-subtitle v-if="!availableToRedacting && countOfUsers.length">Добавлено</v-card-subtitle>
            </v-col>
            <v-col class="col-3 d-flex justify-end align-center">
              <v-btn
                v-if="countOfUsers.length===0"
                @click="addUser"
                color="newPyrple white--text"
                class="px-2 px-md-4"
                min-width="0"
              >
                <span class="d-none d-md-inline">Добавить</span>
                <v-icon class="d-md-none d-xs-inline px-0">mdi-plus</v-icon>
              </v-btn>
              <v-btn
                v-else
                @click="changeRedacting"
                :disabled="availableToSave"
                color="newPyrple white--text"
                class="px-2 px-md-4"
                min-width="0"
              >
                <span class="d-none d-md-inline">Сохранить</span>
                <v-icon class="d-md-none d-xs-inline px-0">mdi-check</v-icon>
              </v-btn>
            </v-col>
          </v-row>
        </v-container>

        <div class="userInfoBlock" v-for="user of countOfUsers" :key="user.number">
          <UserInfo
            v-on:allValidationResolved="validateRequaired"
            v-on:changeOnRedacting="switchOnRedacting"
            v-on:changeArrayOfUploadingUsers="changeArrayOfUploadingUsers"
            :user="user"
          ></UserInfo>
          <v-divider v-if="countOfUsers.length>1" class="mx-3 my-4"></v-divider>
        </div>

        <v-btn
          v-if="countOfUsers.length"
          @click="addUser"
          outlined
          class="newPyrple--text newPyrple--border mb-5"
        >
          <span>+</span> Еще
        </v-btn>
      </v-card>
    </v-main>
  </v-app>
</template>

<script>
import UserInfo from "./components/UserInfo.vue";
export default {
  name: "App",

  components: { UserInfo },

  data: () => ({
    countOfUsers: [],
    uploadingInfo: [],
    arrayOfUploadingUsers: new Set()
  }),
  computed: {
    availableToSave() {
      if (this.countOfUsers.some(user => user.isValidate === false))
        return true;
      if (this.countOfUsers.every(user => !user.isRedact && user.isValidate))
        return true;
      return false;
    },

    availableToRedacting() {
      return this.countOfUsers.some(user => user.isRedact === true)
        ? true
        : false;
    }
  },
  methods: {
    addUser() {
      this.countOfUsers.length === 0
        ? this.countOfUsers.push({
            number: 1,
            isRedact: true,
            isValidate: false
          })
        : this.countOfUsers.push({
            number: this.countOfUsers.length + 1,
            isRedact: true,
            isValidate: false
          });
    },
    changeRedacting() {
      for (let user of this.countOfUsers) {
        user.isRedact = false;
      }

      if (this.arrayOfUploadingUsers.size) {
        let subarr = [];
        this.arrayOfUploadingUsers.forEach(x => subarr.push(x));

        console.log(
          "Changed users: ",
          subarr.map(item => this.countOfUsers[item - 1])
        );
        this.arrayOfUploadingUsers.clear();
      }
    },
    validateRequaired(event) {
      this.countOfUsers.find(user => user.number === event.number).isValidate =
        event.value;
    },
    changeArrayOfUploadingUsers(event) {
      this.arrayOfUploadingUsers.add(event);
    },
    switchOnRedacting(event) {
      this.countOfUsers.find(
        user => user.number === event.number
      ).isRedact = true;
    }
  }
};
</script>

<style lang="scss">
body, html {
  background: #f5f6fb;
  &::-webkit-scrollbar {
    width: 0px;
  }
}

.v-application--wrap {
  background: #f5f6fb;
}

.userInfoBlock {
  width: 100%;
}

.container {
  width: 100vw;
  .v-card__title {
    line-height: 23px;
  }
  input::-webkit-calendar-picker-indicator {
    position: absolute;
    top: 50%;
    left: -26px;
    transform: translateY(-50%);
    opacity: 0;
  }
}
</style>
