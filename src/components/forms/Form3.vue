<template>
  <div>
    <v-flex>
      <v-container class="grey lighten-5">
        <v-row align="center" dense class="flex">
          <FormHeader
            title="Formulário 3"
            subtitle="Aplicação do Instrumento"
            comment="Matriz"
          />
        </v-row>
        <div class="form3">
          <v-row align="center" dense class="flex">
            <v-col :cols="ContentCols">
              Domínios e Atividades
            </v-col>
            <v-col class="text-center" :cols="SelectCols * 2">
              <span>Pontuação INSS</span>
              <Tooltip
                content="Consulte a legenda para mais critérios de preenchimento da pontuação INSS."
                mdiIcon="mdi-comment-question-outline"
              />
            </v-col>
            <v-col class="text-center" :cols="CheckListCols">
              <span>Barreiras Ambientais</span>
              <Tooltip
                content="Consulte a legenda para mais informações a respeito das Barreiras Ambientais."
                mdiIcon="mdi-comment-question-outline"
              />
            </v-col>
          </v-row>
          <div text-wrap v-for="(dominio, i) in Dominios" :key="i">
            {{ i + 1 }}. {{ dominio.Desc }}
            <v-row
              align="center"
              dense
              class="flex"
              v-for="(subdominio, j) in dominio.SubDominios"
              :key="j"
            >
              <v-col class="align-start" :cols="ContentCols">
                <v-textarea
                  :value="subdominio.Detalhe"
                  :label="i + 1 + '.' + (j + 1) + ' ' + subdominio.Desc"
                  rows="1"
                  disabled
                  auto-grow
                  class="align-start"
                ></v-textarea>
              </v-col>
              <v-col class="align-start justify-md-end" :cols="SelectCols">
                <CheckList
                  :inner-items="INSS"
                  inner-label="Médico"
                  :make-outlined="true"
                  @selected-items="refreshScores('medical', i, j, $event)"
                />
              </v-col>
              <v-col class="align-start justify-md-end" :cols="SelectCols">
                <CheckList
                  :inner-items="INSS"
                  inner-label="Social"
                  :make-outlined="true"
                  @selected-items="refreshScores('social', i, j, $event)"
                />
              </v-col>
              <v-col class="align-start" :cols="CheckListCols">
                <!-- Precisa fazer essa logica do .sync para todos os CheckLists-->
                <!-- Se quiser uma trigger de evento sempre que o dado mudar, pode usar: -->
                <!-- @update:selected="myFunction" -->
                <CheckList
                  inner-label="Barreira Ambiental"
                  :selected.sync="barreirasSelecionadas"
                  :inner-items="Barreiras"
                  :allow-multiple="true"
                  :make-outlined="true"
                />
              </v-col>
            </v-row>
            <hr />
          </div>
        </div>
      </v-container>
    </v-flex>
  </div>
</template>
<script>
import Dominios from "@/assets/json/form3.json";
import INSSDesc from "@/assets/json/inss.json";
import BarreirasDesc from "@/assets/json/barreiras.json";
import { mapGetters, mapActions } from "vuex";
export default {
  data: () => ({
    ContentCols: 5,
    SelectCols: 2,
    CheckListCols: 3,
    Barreiras: ["P e T", "Amb", "A e R", "At", "SS e P"],
    barreirasSelecionadas: [],
    Dominios: Object.values(Dominios),
    INSSDesc: Object.values(INSSDesc),
    BarreirasDesc: Object.values(BarreirasDesc),
    INSS: ["25", "50", "75", "100"]
  }),
  components: {
    FormHeader: () => import("@/components/forms/FormHeader"),
    CheckList: () => import("@/components/CheckList"),
    Tooltip: () => import("@/components/Tooltip")
  },
  methods: {
    ...mapActions([
      "setScores",
      "updateScores",
      "cycleScores",
      "calcScores",
      "makeFuzzy"
    ]),
    refreshScores(col, i, j, value) {
      const update = { col: col, i: i, j: j, value: value };
      this.updateScores(update);
      this.cycleScores(update);
      if (this.filledStatus) {
        this.calcScores();
      }
    }
  },
  computed: {
    ...mapGetters(["filledStatus", "allScores"])
  },
  created() {
    this.setScores(Dominios);
  }
};
</script>

<style>
:disabled {
  color: black !important;
}
v-textarea {
  border-style: none;
}
</style>
