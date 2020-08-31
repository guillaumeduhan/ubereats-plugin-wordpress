<template>
  <div id="app" class="pt-3">
    <div
      class="Simulateur container p-0 d-flex flex-column align-items-start justify-content-start border rounded"
      :style="{
        borderColor: '#ee6366!important',
        maxWidth: '580px',
        minHeight: '340px',
      }"
    >
      <div
        class="p-3 py-1 w-100"
        :style="{ backgroundColor: '#ee6366!important', color: 'white' }"
      >
        <div class="mb-0 d-flex align-items-center justify-centent-start">
          <img
            src="https://d3i4yxtzktqr9n.cloudfront.net/web-eats-v2/0c6de4f0b3884eb89b28a29ecbc10d59.svg"
            alt="simulateur"
            class="rounded-circle mr-3"
            :style="{ maxWidth: '40px' }"
          />
          Simulateur de revenus Uber Eats
        </div>
      </div>
      <div
        class="Slides p-3 flex-grow-1"
        v-if="slides[currentStep]"
        :style="getStyle"
      >
        <p class="font-weight-bold">{{ slides[currentStep].title }}</p>
        <div
          v-if="slides[currentStep].input"
          class="d-flex flex-column align-items-start justify-content-between"
        >
          <p
            v-if="slides[currentStep].input.title"
            class="d-flex align-items-center justify-content-between mr-4"
          >
            <span
              v-if="slides[currentStep].input.type === 'range1'"
              class="mr-2"
              >{{ km }}</span
            >
            <span>{{ slides[currentStep].input.title }}</span>
            <span
              v-if="slides[currentStep].input.type === 'range2'"
              class="ml-2"
              >{{ getCoef }}</span
            >
            <input
              v-if="slides[currentStep].input.type === 'commands'"
              v-model="commands"
              class="ml-2"
              type="number"
              style="max-width: 50px;"
              min="0"
            />
          </p>
          <input
            v-if="slides[currentStep].input.type === 'range1'"
            type="range"
            class="custom-range"
            min="0"
            max="150"
            id="customRange2"
            v-model="km"
          />
          <input
            v-if="slides[currentStep].input.type === 'range2'"
            type="range"
            class="custom-range"
            min="10"
            max="20"
            id="customRange3"
            v-model="coef"
          />
          <div v-if="slides[currentStep].input.type === 'multi'">
            <div
              v-for="(check, indexCheck) in slides[currentStep].input.checks"
              :key="indexCheck"
              class="form-check mr-2"
            >
              <input
                v-if="slides[currentStep].input.model === 'place'"
                class="form-check-input"
                type="radio"
                :id="check.name"
                :name="check.name"
                :value="check.value"
                v-model="place"
              />
              <input
                v-if="slides[currentStep].input.model === 'accre'"
                class="form-check-input"
                type="radio"
                :id="check.name"
                :name="check.name"
                :value="check.value"
                v-model="accre"
              />
              <label class="form-check-label" :for="check.name">{{
                check.description
              }}</label>
            </div>
            <div
              v-if="slides[currentStep].input.model === 'accre'"
              class="d-flex flex-column mt-2"
            >
              <label for="example-date-input" class="col-form-label"
                >Date d'obtention:</label
              >
              <input
                class="form-control"
                type="date"
                v-model="start_date"
                id="example-date-input"
              />
            </div>
            <div
              v-if="slides[currentStep].input.model === 'accre'"
              class="my-3"
            >
              <a
                href="https://hellomybusiness.fr/la-demande-acre-2/"
                target="_blank"
                >Suis-je éligible à l'ACCRE?</a
              >
            </div>
          </div>
          <div
            v-if="slides[currentStep].input.type === 'bonus'"
            class="input-group mb-3"
            style="max-width: 100px;"
          >
            <input type="text" class="form-control" v-model="bonus" min="0" />
            <div class="input-group-append">
              <span class="input-group-text" id="basic-addon2">€</span>
            </div>
          </div>
          <div class="d-flex" v-if="slides[currentStep].input.type === 'tips'">
            <input
              type="range"
              class="custom-range mr-2"
              min="0"
              max="50"
              id="customRange2"
              style="color: #ee6366;"
              v-model="tips"
            />
            <label for="customRange2" style="min-width: 50px;"
              >{{ tips }} €</label
            >
          </div>
        </div>
        <div v-else :class="getStyle">
          <p
            class="alert alert"
            role="alert"
            :style="{
              backgroundColor: 'rgba(33, 236, 144, 0.9)',
              color: 'black',
            }"
          >
            Total gain :
            <span class="font-weight-bold">{{ getTotalWithTaxes }} €</span>
          </p>
          <div class="d-flex align-items-center justify-content-between">
            <p class="mb-0 mr-2">Combien d'heures avez vous travaillé ?</p>
            <input type="number" v-model="hours" style="max-width: 50px;" />
          </div>
          <p>
            Revenu net par heure :
            {{ getHours !== Infinity ? getHours : 0 }} €
          </p>
        </div>
        <p class="small font-italic mt-2" v-if="slides[currentStep].note">
          {{ slides[currentStep].note }}
        </p>
      </div>
      <div class="Buttons pl-3 pb-3">
        <button
          v-if="currentStep > 0"
          type="button"
          class="btn mr-2"
          :style="getButtonStyle"
          @click="currentStep--"
        >
          Précédent
        </button>
        <button
          v-if="currentStep + 1 < slides.length"
          class="btn btn-primary"
          type="button"
          :style="getButtonStyle"
          @click="currentStep++"
        >
          Suivant
        </button>
        <button
          v-if="currentStep + 1 === slides.length"
          type="button"
          class="btn btn-primary"
          :style="getButtonStyle"
          @click="currentStep = 0"
        >
          Recommencer
        </button>
      </div>
      <div
        class="d-flex align-items-center justify-content-center px-3 py-2"
        type="button"
        :style="{ backgroundColor: '#18223B', width: '100%', color: 'white' }"
        @click="openUrl"
      >
        <p class="small pr-2 m-0">Outil développé par:</p>
        <img
          :style="{ maxHeight: '25px' }"
          src="https://hellomybusiness.fr/wp-content/uploads/2020/04/Capture-d’écran-2020-04-07-à-17.08.01.png"
          alt="Hello My Business"
        />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'appvue',
  computed: {
    getButtonStyle() {
      return 'background-color: #ee6366; color: white; border: 1px solid #ee6366; outline: 0px; box-shadow: none!important;';
    },
    getCoef() {
      return this.coef / 10;
    },
    getHours() {
      if (this.hours === 0) return 0;
      return Math.round(this.getTotalWithTaxes / this.hours);
    },
    getStyle() {
      return 'max-width: 400px;';
    },
    getTotalWithTaxes() {
      if (!this.getTotal) return;
      let startYear = moment(this.start_date);
      let currentYear = moment();
      let twenty = moment('2020');
      let p = 0;
      let regular = 22;

      if (!this.accre) {
        p = regular;
      } else {
        if (startYear.format('YYYY') < twenty.format('YYYY')) {
          switch (startYear.diff(currentYear, 'years')) {
            case 0:
              p = 5.5;
              break;
            case -1:
              p = 11;
              break;
            case -2:
              p = 16.5;
              break;
            default:
              p = regular;
          }
        } else {
          switch (startYear.diff(currentYear, 'years')) {
            case 0:
              p = 11;
              break;
            default:
              p = regular;
          }
        }
      }
      return Math.round(this.getTotal - (this.getTotal * p) / 100);
    },
    getTotal() {
      let m = this.place === 'Paris' ? 0.81 : 0.76;
      let r = (this.commands * 2.85 + this.km * m) * this.getCoef;
      let b = this.bonus ? parseInt(this.bonus) : 0;
      let t = this.tips ? parseInt(this.tips) : 0;
      return r + b + t;
    },
  },
  data() {
    return {
      accre: false,
      bonus: 0,
      coef: 10,
      commands: 1,
      currentStep: 0,
      hours: 0,
      km: 0,
      place: 'Paris',
      slides: [
        {
          input: {
            id: 'input1',
            title: 'Nbr de commandes récupérées et livrées :',
            type: 'commands',
          },
          note:
            'La récupération d’une commande est rémunérée 1,90€ et la remise au client 0,95€.',
          title: 'Tarification de base',
        },
        {
          input: {
            checks: [
              {
                name: 'name1',
                description: 'A Paris',
                value: 'Paris',
              },
              {
                name: 'name2',
                description: 'Ville de province',
                value: 'Province',
              },
            ],
            id: 'input2',
            model: 'place',
            type: 'multi',
          },
          note:
            'Le kilomètre parcouru pendant votre course est rémunéré à Paris 0,81€/km et en province 0,76€/km',
          title: 'Où effectuez-vous vos livraisons ?',
        },
        {
          input: {
            id: 'input3',
            title: 'kilomètres',
            type: 'range1',
          },
          title: 'Nbr de kilomètres parcourus',
        },
        {
          input: {
            id: 'input4',
            title: 'Coefficient multiplicateur:',
            type: 'range2',
          },
          note:
            "Certaines zones bénéficient d\’un coefficient multiplicateur. Il s\’applique au prix de la course en fonction du lieu et de l'heure de la commande.",
          title: 'Tarification additionelle',
        },
        {
          input: {
            id: 'input5',
            title: 'Montant du bonus:',
            type: 'bonus',
          },
          note:
            'Uber active régulièrement des bonus pendant les pics de commandes. Ces bonus peuvent varier selon la ville et la saison.',
          title: 'Bonus',
        },
        {
          input: {
            id: 'input6',
            title: 'Montant du pourboire:',
            type: 'tips',
          },
          title: 'Pourboires',
        },
        {
          input: {
            checks: [
              {
                name: 'name3',
                description: 'Oui',
                value: true,
              },
              {
                name: 'name4',
                description: 'Non',
                value: false,
              },
            ],
            id: 'input7',
            model: 'accre',
            title: 'Je bénéficie de l’accre :',
            type: 'multi',
          },
          title: 'Cotisations sociales',
        },
        {
          title: 'Total revenus livreur',
        },
      ],
      start_date: '2020-06-01',
      tips: 0,
    };
  },
  methods: {
    openUrl() {
      window.open('https://hellomybusiness.fr/', '_blank');
    },
  },
  mounted() {
    // All animations will take half the time to accomplish
    document.documentElement.style.setProperty('--animate-duration', '2s');
  },
};
</script>
