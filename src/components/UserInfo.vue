<template>
  <v-container white fluid>
    <div v-if="user.isRedact">
      <v-row>
        <span
          class="newPyrple--text pl-4 text-h7 font-weight-bold"
        >Позьзователь №{{user.number}}</span>
      </v-row>

      <v-row>
        <v-col class="col-12 col-sm-4">
          <v-text-field hide-details dense outlined color="newPyrple" v-model="name" label="ФИО"></v-text-field>
        </v-col>
        <v-col class="col-12 col-sm-4">
          <v-text-field
          hide-details
          dense
            outlined
            color="newPyrple"
            type="date"
            v-model="date"
            prepend-inner-icon="mdi-calendar"
            label="Дата рождения"
          ></v-text-field>
        </v-col>
        <v-col class="col-12 col-sm-4">
          <v-text-field
          hide-details
          dense
            outlined
            color="newPyrple"
            class="maskPasport"
            v-model="pasport"
            label="Серия и номер паспорта"
          ></v-text-field>
        </v-col>
      </v-row>

      <v-row>
        <v-col class="col-12 col-sm-4">
          <v-text-field
          hide-details
          dense outlined color="newPyrple" class="maskSnils" v-model="snils" label="СНИЛС"></v-text-field>
        </v-col>
        <v-col class="col-12 col-sm-4">
          <v-text-field
          hide-details
          dense
            outlined
            color="newPyrple"
            class="maskTelephone"
            v-model="telephone"
            label="Номер телефона"
          ></v-text-field>
        </v-col>
        <v-col class="col-12 col-sm-4">
          <v-text-field
          hide-details
          dense class="maskEnn" outlined color="newPyrple" v-model="enn" label="ИНН"></v-text-field>
        </v-col>
      </v-row>
    </div>

    <div v-else class="pl-4">
      <v-row style="position: relative" class="d-flex justify-space-between">
        <span class="newPyrple--text mb-4 text-h7 font-weight-bold">Позьзователь №{{user.number}}</span>
        <v-btn
          @click="changeOnRedacting"
          absolute
          top
          right
          width="36"
          color="#EEEEEE"
          height="36"
          class="px-2"
          min-width="0"
        >
          <v-icon>mdi-pencil</v-icon>
        </v-btn>
      </v-row>
      <v-row class="d-flex flex-column">
        <article class="d-flex flex-column">
          <span class="newGrey--text">ФИО:</span>
          <span>{{ name }}</span>
        </article>
        <article class="d-flex flex-column">
          <span class="newGrey--text">СНИЛС:</span>
          <span>{{snils }}</span>
        </article>
        <article class="d-flex flex-column">
          <span class="newGrey--text">Номер телефона:</span>
          <span>{{ telephone }}</span>
        </article>
        <article class="d-flex flex-column">
          <span class="newGrey--text">ИНН:</span>
          <span>{{ enn }}</span>
        </article>
        <article class="d-flex flex-column">
          <span class="newGrey--text">Дата рождения:</span>
          <span>{{ date}}</span>
        </article>
        <article class="d-flex flex-column">
          <span class="newGrey--text">Серия и номер паспорта:</span>
          <span>{{ pasport }}</span>
        </article>
      </v-row>
    </div>
  </v-container>
</template>

<script>
import Inputmask from "inputmask";
export default {
  name: "UserInfo",
  props: ["user"],
  data: () => ({
    name: "",
    date: "",
    pasport: "",
    snils: "",
    telephone: "",
    enn: "",
    validation: {
      name: false,
      date: false,
      pasport: false,
      snils: false,
      telephone: false,
      enn: false
    },
  }),
  mounted() {
    ////////маски для инпутов
    let maskPasport = new Inputmask("99 99 999999");
    for (let element of document.getElementsByClassName("maskPasport")) {
      maskPasport.mask(element.getElementsByTagName("input")[0]);
    }

    let maskSnils = new Inputmask("999-999-999 99");
    for (let element of document.getElementsByClassName("maskSnils")) {
      maskSnils.mask(element.getElementsByTagName("input")[0]);
    }

    let maskTelephone = new Inputmask("+7 (999) 999-99-99");
    for (let element of document.getElementsByClassName("maskTelephone")) {
      maskTelephone.mask(element.getElementsByTagName("input")[0]);
    }

    let maskEnn = new Inputmask("999999999999");
    for (let element of document.getElementsByClassName("maskEnn")) {
      maskEnn.mask(element.getElementsByTagName("input")[0]);
    }
  },

  computed: {
    allValidationResolved() {
      if (
        this.validation.name &&
        this.validation.date &&
        this.validation.pasport &&
        this.validation.snils &&
        this.validation.telephone &&
        this.validation.enn
      ) {
        return true;
      } else {
        return false;
      }
    }
  },
  methods: {
    changeOnRedacting() {
      this.$emit("changeOnRedacting", { number: this.user.number });
    },

    validateName(value) {
      value !== ""
        ? (this.validation.name = true)
        : (this.validation.name = false);
    },
    validateDate(value) {
      value !== ""
        ? (this.validation.date = true)
        : (this.validation.date = false);
    },
    validatePasport(value) {
      /^[0-9]{2}\s[0-9]{2}\s[0-9]{6}/g.test(value)
        ? (this.validation.pasport = true)
        : (this.validation.pasport = false);
    },
    validateSnils(value) {
      /^[0-9]{3}[-][0-9]{3}[-][0-9]{3}\s[0-9]{2}/g.test(value)
        ? (this.validation.snils = true)
        : (this.validation.snils = false);
    },
    validateTelephone(value) {
      /^\+[7]\s[(][0-9]{3}[)]\s[0-9]{3}[-][0-9]{2}[-][0-9]{2}/g.test(value)
        ? (this.validation.telephone = true)
        : (this.validation.telephone = false);
    },
    validateEnn(value) {
      /^[0-9]{12}/g.test(value)
        ? (this.validation.enn = true)
        : (this.validation.enn = false);
    }
  },
  watch: {
    name(value) {
      this.validateName(value);
      this.$emit("changeArrayOfUploadingUsers", this.user.number);
    },
    date(value) {
      this.validateDate(value);
      this.$emit("changeArrayOfUploadingUsers", this.user.number);
    },
    snils(value) {
      this.validateSnils(value);
      this.$emit("changeArrayOfUploadingUsers", this.user.number);
    },
    pasport(value) {
      this.validatePasport(value);
      this.$emit("changeArrayOfUploadingUsers", this.user.number);
    },
    telephone(value) {
      this.validateTelephone(value);
      this.$emit("changeArrayOfUploadingUsers", this.user.number);
    },
    enn(value) {
      this.validateEnn(value);
      this.$emit("changeArrayOfUploadingUsers", this.user.number);
    },
    allValidationResolved(value) {
      this.$emit("allValidationResolved", {
        number: this.user.number,
        value
      });
    }
  }
};
</script>

