<template>
    <v-card>
        <v-card-text>
            <v-text-field
                label="Last Name"
                v-model="person.lastName"
                required
            ></v-text-field>
            <v-text-field
                label="Cell Number"
                v-model="person.cell"
                :counter="10"
                mask="phone"
            ></v-text-field>
            <v-text-field
                label="Land Line"
                v-model="person.landLine"
                mask="(###) ### ####"
            ></v-text-field>
            <v-text-field
                label="ID Number"
                persistent-hint=""
                hint="Required if you want to sell through us"
                v-model="person.idSA"
                :counter="13"
                mask="###### #### ###"
            ></v-text-field>         
            <v-layout row justify-center>
                <v-dialog v-if="idData !== null"  v-model="dialog" persistent max-width="290">
                    <v-card>
                        <v-card-title class="headline">Checking Integrity of ID Number</v-card-title>
                        <v-card-text>Are you a <strong>{{ idData.gender}}</strong>, with a birthdate of <strong>{{idData.birthDate}}</strong> and a <strong>{{ idData.isZaCitizen ? "Citizen" : "Non-Citizen"}}</strong> of South Africa? If not, please re-check your ID number below.</v-card-text>
                        <v-card-actions>
                            <v-spacer></v-spacer>
                            <v-btn color="primary" flat @click.native="dialog = false">OK</v-btn>
                            <v-spacer></v-spacer>
                        </v-card-actions>
                    </v-card>
                </v-dialog>
            </v-layout>
        </v-card-text>
        <v-btn
            color="primary"
            @click="$store.dispatch('changeElement', 2); submit()"
        >
            Save Draft
        </v-btn> 
    </v-card>
</template>

<script>
import { PERSONALDETAILS_MUTATION } from "@/graphql/mutations";
import { idDataExtraction } from '@/helpers/idDataExtraction'

export default {
    mounted() {
        if(this.$store.getters.personalDetails !== null) {
            this.person = this.$store.getters.personalDetails
        }
    },
    data: () => ({
        dialog: false,
        idData: null,
        person: {
            lastName: null,
            cell: null,
            landLine: null,
            idSA: null,
        }
    }),
    computed: {
        idSA() {
            return this.person.idSA
        },
    },
    methods: {
        checkID(val) {
            this.idData = idDataExtraction(val)
            this.dialog = true
        },
        submit() {
            this.$store.dispatch('personalDetails', this.person)
        }
    },
    watch: {
        idSA(newVal) {
            if(newVal.length === 13) {
                this.checkID(newVal)
            }
        },
    }
};
// submit() {
//   this.$apollo
//     .mutate({
//       mutation: PERSONALDETAILS_MUTATION,
//       variables: {
//         lastName: this.person.lastName,
//         cellNo: this.person.cellNo,
//         landLine: this.person.landLine,
//         idSA: this.person.idSA
//       }
//     })
//     .then(response => {
//       console.log("​login -> response.data", response.data);
//     })
//     .catch(error => console.error(error));
// }
</script>

