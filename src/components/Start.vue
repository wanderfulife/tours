<template>
	<div class="container">

		<div id="enqueteur" v-if="level === 0">
			<h2>Prénom enqueteur :</h2>
			<input class="form-control" type="text" v-model="ENQUETEUR" />
			<button v-if="ENQUETEUR" @click="next" class="btn-next">Suivant</button>
		</div>

		<div v-if="level === 1">
			<button @click="startSurvey" class="btn-next">COMMENCER QUESTIONNAIRE</button>
		</div>

		<div id="q1" v-if="level === 2">
			<h2> Questionnaire </h2>
			<select v-model="Q1" class="form-control">
				<option v-for="option in q1" :key="option.id" :value="option.output">
					{{ option.text }}
				</option>
			</select>
			<button v-if="Q1" @click="next" class="btn-next">Suivant</button>
			<button @click="back" class="btn-return">retour</button>
		</div>

		<div id="q2" v-if="level === 3">
			<h2>De quelle commune venez-vous ?</h2>
			<div>
				<CommuneSelector v-model="Q2" />
			</div>
			<div v-if="Q2 === 'TOURS - 37261'">
				<h2>Precisez le quartier</h2>
				<select v-model="Q2qt" class="form-control">
					<option v-for="option in tours" :key="option.id" :value="option.output">
						{{ option.text }}
					</option>
				</select>
			</div>
			<div v-if="Q2 === 'ORLEANS - 45234'">
				<h2>Precisez le quartier</h2>
				<select v-model="Q2qo" class="form-control">
					<option v-for="option in orleans" :key="option.id" :value="option.output">
						{{ option.text }}
					</option>
				</select>
			</div>
			<button v-if="Q2" @click="next" class="btn-next">Suivant</button>
			<button @click="back" class="btn-return">retour</button>
		</div>

		<div id="DESTINATION" v-if="level === 4 && Q1 === 2">
			<h2>Pour ce trajet en train (car), quelle a été votre gare SNCF (arrêt) de montée ?</h2>
			<div>
				<GareSelector v-model="DESTINATION" />
			</div>
			<br>
			<h2>Ou</h2>
			<h2>Pour ce trajet en train (car), quelle a été votre gare SNCF (arrêt) de montée ?</h2>
			<input class="form-control" type="text" v-model="DESTINATION" placeholder="Arret Remi">
			<button v-if="DESTINATION" @click="next" class="btn-next">Suivant</button>
			<button @click="back" class="btn-return">retour</button>
		</div>

		<div id="MORIGINE" v-if="level === 4 && Q1 === 1||level === 5 && Q1 === 2">
			<h2>Ce lieu est-il …. ? </h2>
			<select v-model="MORIGINE" class="form-control">
				<option v-for="option in motif" :key="option.id" :value="option.output">
					{{ option.text }}
				</option>
			</select>
			<input v-if="MORIGINE === 8" class="form-control" type="text" v-model="MORIGINE_DETAIL"
				placeholder="Precisions">
			<button v-if="MORIGINE" @click="next" class="btn-next">Suivant</button>
			<button @click="back" class="btn-return">retour</button>
		</div>

		<div id="MRABATTEMENT_A" v-if="level === 5 && Q1 === 1 || level === 6 && Q1 === 2">
			<h2>Par quel mode de transport principal etes-vous arrivés la gare SNCF (ou routière) ?</h2>
			<select v-model="MRABATTEMENT_A" class="form-control">
				<option v-for="option in mode" :key="option.id" :value="option.output">
					{{ option.text }}
				</option>
			</select>
			<input v-if="MRABATTEMENT_A === 9" class="form-control" type="text" v-model="MRABATTEMENT_A_DETAIL"
				placeholder="Precisions">
			<button v-if="MRABATTEMENT_A" @click="next" class="btn-next">Suivant</button>
			<button @click="back" class="btn-return">retour</button>
		</div>

		<div id="DESTINATION" v-if="level === 6 && Q1 === 1">
			<h2>Pour ce trajet en train, quelle sera votre gare de descente ?</h2>
			<div>
				<GareSelector v-model="DESTINATION" />
			</div>
			<br>
			<h2>Ou</h2>
			<h2>Pour ce trajet en car, quelle sera votre arret de descente ?</h2>
			<input class="form-control" type="text" v-model="DESTINATION" placeholder="Arret Remi">
			<button v-if="DESTINATION" @click="next" class="btn-next">Suivant</button>
			<button @click="back" class="btn-return">retour</button>
		</div>



		<div id="MOTIF_DESTINATION" v-if="level === 7 && Q1 === 1 || level === 8 && Q1 === 2">
			<h2>Ce lieu est-il …. ? </h2>
			<select v-model="MOTIF_DESTINATION" class="form-control">
				<option v-for="option in motif" :key="option.id" :value="option.output">
					{{ option.text }}
				</option>
			</select>
			<input v-if="MOTIF_DESTINATION === 8" class="form-control" type="text" v-model="MOTIF_DESTINATION_DETAIL"
				placeholder="Precisions">
			<button v-if="MOTIF_DESTINATION" @click="next" class="btn-next">Suivant</button>
			<button @click="back" class="btn-return">retour</button>
		</div>

		<div id="DEST_FIN" v-if="level === 8 && Q1 === 1 ||level === 7 && Q1 === 2">
			<h2>Quelle est votre destination finale à la descente du train (car) ?</h2>
			<div>
				<CommuneSelector v-model="DEST_FIN" />
			</div>
			<div v-if="DEST_FIN === 'TOURS - 37261'">
				<h2>Precisez le quartier</h2>
				<select v-model="DEST_FINqt" class="form-control">
					<option v-for="option in tours" :key="option.id" :value="option.output">
						{{ option.text }}
					</option>
				</select>
			</div>
			<div v-if="DEST_FIN === 'ORLEANS - 45234'">
				<h2>Precisez le quartier</h2>
				<select v-model="DEST_FINqo" class="form-control">
					<option v-for="option in orleans" :key="option.id" :value="option.output">
						{{ option.text }}
					</option>
				</select>
			</div>
			<button v-if="DEST_FIN" @click="next" class="btn-next">Suivant</button>
			<button @click="back" class="btn-return">retour</button>
		</div>

		<div id="MRABATTEMENT_S" v-if="level === 9 && Q1 === 1 || level === 9 && Q1 === 2">
			<h2>Par quel mode de transport allez-vous quittez la gare SNCF (ou routière) ? </h2>
			<select v-model="MRABATTEMENT_S" class="form-control">
				<option v-for="option in mode" :key="option.id" :value="option.output">
					{{ option.text }}
				</option>
			</select>
			<input v-if="MRABATTEMENT_S === 9" class="form-control" type="text" v-model="MRABATTEMENT_S_DETAIL"
				placeholder="Precisions">
			<button v-if="MRABATTEMENT_S" @click="next" class="btn-next">Suivant</button>
			<button @click="back" class="btn-return">retour</button>
		</div>


		<div id="q9" v-if="level === 10 ">
			<h2>A quelle fréquence faites-vous le même trajet qu'aujourd'hui ? </h2>
			<select v-model="Q9" class="form-control">
				<option v-for="option in frequence" :key="option.id" :value="option.output">
					{{ option.text }}
				</option>
			</select>
			<input v-if="Q9 === 8" class="form-control" type="text" v-model="Q9_DETAIL" placeholder="Precisions">
			<button v-if="Q9" @click="next" class="btn-next">Suivant</button>
			<button @click="back" class="btn-return">retour</button>
		</div>

		<div id="q10" v-if="level === 11">
			<h2>Quelle est votre titre de transport ?</h2>
			<select v-model="Q10" class="form-control">
				<option v-for="option in titre" :key="option.id" :value="option.output">
					{{ option.text }}
				</option>
			</select>
			<button v-if="Q10" @click="next" class="btn-next">Suivant</button>
			<button @click="back" class="btn-return">retour</button>
		</div>

		<div id="q11" v-if="level === 12">
			<h2>Sur quel type de support ?</h2>
			<select v-model="Q11" class="form-control">
				<option v-for="option in support" :key="option.id" :value="option.output">
					{{ option.text }}
				</option>
			</select>
			<button v-if="Q11" @click="next" class="btn-next">Suivant</button>
			<button @click="back" class="btn-return">retour</button>
		</div>

		<div id="q12" v-if="level === 13">
			<h2>L'interviewé avait-il un(e) vélo/trottinette/mono roue ?</h2>
			<select v-model="Q12" class="form-control">
				<option v-for="option in q12" :key="option.id" :value="option.output">
					{{ option.text }}
				</option>
			</select>
			<button v-if="Q12" @click="next" class="btn-next">Suivant</button>
			<button @click="back" class="btn-return">retour</button>
		</div>

		<div id="q13" v-if="level === 14">
			<h2>Noter le numéro du quai</h2>
			<input class="form-control" type="text" v-model="Q13" placeholder="Precisions">
			<button v-if="Q13" @click="next" class="btn-next">Suivant</button>
			<button @click="back" class="btn-return">retour</button>
		</div>

		<div id="q14" v-if="level === 15">
			<h2>Noter le numéro du train/car </h2>
			<input class="form-control" type="text" v-model="Q14" placeholder="Precisions">
			<button v-if="Q14" @click="next" class="btn-next">Suivant</button>
			<button @click="back" class="btn-return">retour</button>
		</div>

		<div id="end" v-if="level === 16">
			<button @click="submitSurvey" class="btn-next">FINIR QUESTIONNAIRE</button>
			<button @click="back" class="btn-return">retour</button>
		</div>
		<img class="logo" src="../assets/Alycelogo.webp" alt="Logo Alyce">

		<!-- <button class="btn-fin" @click="downloadData">download DATA</button> -->

	</div>
	<div>
		<span style="margin-left: 10px;">Nombre de questionnaires : {{ docCount }}</span>
	</div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { q1, motif, mode, frequence, titre, support, tours, orleans, q12 } from "./reponses";
import GareSelector from "./GareSelector.vue";
import CommuneSelector from './CommuneSelector.vue';
import { db } from "../firebaseConfig";
import { collection, getDocs, addDoc } from "firebase/firestore";
import * as XLSX from "xlsx";

const docCount = ref(0);
const surveyCollectionRef = collection(db, "Tours");
const level = ref(0);
const startDate = ref('');
const ENQUETEUR = ref('');
const Q1 = ref('');
const Q2 = ref('');
const Q2qt = ref('');
const Q2qo = ref('');
const MORIGINE = ref('');
const MORIGINE_DETAIL = ref('');
const DESTINATION = ref('');
const MOTIF_DESTINATION = ref('');
const MOTIF_DESTINATION_DETAIL = ref('');
const DEST_FIN = ref('');
const DEST_FINqt = ref('');
const DEST_FINqo = ref('');
const MRABATTEMENT_A = ref('');
const MRABATTEMENT_A_DETAIL = ref('');
const MRABATTEMENT_S = ref('');
const MRABATTEMENT_S_DETAIL = ref('');
const Q9 = ref('');
const Q9_DETAIL = ref('');
const Q10 = ref('');
const Q11 = ref('');
const Q12 = ref('');
const Q13 = ref('');
const Q14 = ref('');



const startSurvey = () => {
	startDate.value = new Date().toLocaleTimeString("fr-FR").slice(0, 8);
	level.value++;
}


const next = () => {
	level.value++;
	console.log(level.value)
}

const back = () => {
	level.value--;
}


const getDocCount = async () => {
	try {
		const querySnapshot = await getDocs(surveyCollectionRef);
		docCount.value = querySnapshot.size;
	} catch (error) {
		console.error("Error getting document count:", error);
	}
};

onMounted(getDocCount);

const submitSurvey = async () => {
	await addDoc(surveyCollectionRef, {
		HEURE_DEBUT: startDate.value,
		DATE: new Date().toLocaleDateString("fr-FR").replace(/\//g, "-"),
		JOUR: new Date().toLocaleDateString("fr-FR", { weekday: 'long' }),
		ENQUETEUR: ENQUETEUR.value,
		HEURE_FIN: new Date().toLocaleTimeString("fr-FR").slice(0, 8),
		Q1: Q1.value,
		Q2: Q2.value,
		Q2qt: Q2qt.value,
		Q2qo: Q2qo.value,
		MORIGINE: MORIGINE.value,
		MORIGINE_DETAIL: MORIGINE_DETAIL.value,
		DESTINATION: DESTINATION.value,
		MOTIF_DESTINATION: MOTIF_DESTINATION.value,
		MOTIF_DESTINATION_DETAIL: MOTIF_DESTINATION_DETAIL.value,
		DEST_FIN: DEST_FIN.value,
		DEST_FINqt: DEST_FINqt.value,
		DEST_FINqo: DEST_FINqo.value,
		MRABATTEMENT_A: MRABATTEMENT_A.value,
		MRABATTEMENT_A_DETAIL: MRABATTEMENT_A_DETAIL.value,
		MRABATTEMENT_S: MRABATTEMENT_S.value,
		MRABATTEMENT_S_DETAIL: MRABATTEMENT_S_DETAIL.value,
		Q9: Q9.value,
		Q9_DETAIL: Q9_DETAIL.value,
		Q10: Q10.value,
		Q11: Q11.value,
		Q12: Q12.value,
		Q13: Q13.value,
		Q14: Q14.value,

	});
	level.value = 1;
	startDate.value = "";
	getDocCount();
	Q1.value = "";
	Q2.value = "";
	Q2qt.value = "";
	Q2qo.value = "";
	MORIGINE.value = "";
	MORIGINE_DETAIL.value = "";
	DESTINATION.value = "";
	MOTIF_DESTINATION.value = "";
	MOTIF_DESTINATION_DETAIL.value = "";
	DEST_FIN.value = "";
	DEST_FINqt.value = "";
	DEST_FINqo.value = "";
	MRABATTEMENT_A.value = "";
	MRABATTEMENT_A_DETAIL.value = "";
	MRABATTEMENT_S.value = "";
	MRABATTEMENT_S_DETAIL.value = "";
	Q9.value = "";
	Q9_DETAIL.value = "";
	Q10.value = "";
	Q11.value = "";
	Q12.value = "";
	Q13.value = "";
	Q14.value = "";
};

const downloadData = async () => {
	try {
		const querySnapshot = await getDocs(surveyCollectionRef);
		let data = [];
		let maxWidths = {}; // Object to keep track of maximum width for each column

		// Define your headers // MODIFICATION SUR L'EXCEL
		const headers = {
			ID_questionnaire: "ID_questionnaire",
			ENQUETEUR: "ENQUETEUR",
			DATE: "DATE",
			JOUR: "JOUR",
			HEURE_DEBUT: "HEURE_DEBUT",
			HEURE_FIN: "HEURE_FIN",
			Q1: "Q1",
			Q2: "Q2",
			Q2qt: "Q2qt",
			Q2qo: "Q2qo",
			MORIGINE: "MORIGINE",
			MORIGINE_DETAIL: "MORIGINE_DETAIL",
			DESTINATION: "DESTINATION",
			MOTIF_DESTINATION: "MOTIF_DESTINATION",
			MOTIF_DESTINATION_DETAIL: "MOTIF_DESTINATION_DETAIL",
			DEST_FIN: "DEST_FIN",
			DEST_FINqt: "DEST_FINqt",
			DEST_FINqo: "DEST_FINqo",
			MRABATTEMENT_A: "MRABATTEMENT_A",
			MRABATTEMENT_A_DETAIL: "MRABATTEMENT_A_DETAIL",
			MRABATTEMENT_S: "MRABATTEMENT_S",
			MRABATTEMENT_S_DETAIL: "MRABATTEMENT_S_DETAIL",
			Q9: "Q9",
			Q9_DETAIL: "Q9_DETAIL",
			Q10: "Q10",
			Q11: "Q11",
			Q12: "Q12",
			Q13: "Q13",
			Q14: "Q14",
		};

		// Initialize maxWidths with header lengths
		Object.keys(headers).forEach((key) => {
			maxWidths[key] = headers[key].length;
		});

		querySnapshot.forEach((doc) => {
			let docData = doc.data();
			let mappedData = {
				ID_questionnaire: doc.id,
				ENQUETEUR: docData.ENQUETEUR || "",
				DATE: docData.DATE || "",
				JOUR: docData.JOUR || "",
				HEURE_DEBUT: docData.HEURE_DEBUT || "",
				HEURE_FIN: docData.HEURE_FIN || "",
				Q1: docData.Q1 || "",
				Q2: docData.Q2 || "",
				Q2qt: docData.Q2qt || "",
				Q2qo: docData.Q2qo || "",
				MORIGINE: docData.MORIGINE || "",
				MORIGINE_DETAIL: docData.MORIGINE_DETAIL || "",
				DESTINATION: docData.DESTINATION || "",
				MOTIF_DESTINATION: docData.MOTIF_DESTINATION || "",
				MOTIF_DESTINATION_DETAIL: docData.MOTIF_DESTINATION_DETAIL || "",
				DEST_FIN: docData.DEST_FIN || "",
				DEST_FINqt: docData.DEST_FINqt || "",
				DEST_FINqo: docData.DEST_FINqo || "",
				MRABATTEMENT_A: docData.MRABATTEMENT_A || "",
				MRABATTEMENT_A_DETAIL: docData.MRABATTEMENT_A_DETAIL || "",
				MRABATTEMENT_S: docData.MRABATTEMENT_S || "",
				MRABATTEMENT_S_DETAIL: docData.MRABATTEMENT_S_DETAIL || "",
				Q9: docData.Q9 || "",
				Q9_DETAIL: docData.Q9_DETAIL || "",
				Q10: docData.Q10 || "",
				Q11: docData.Q11 || "",
				Q12: docData.Q12 || "",
				Q13: docData.Q13 || "",
				Q14: docData.Q14 || "",
			};
			data.push(mappedData);
			// Update maxWidths for each key in mappedData
			Object.keys(mappedData).forEach((key) => {
				const valueLength = mappedData[key].toString().length;
				maxWidths[key] = Math.max(maxWidths[key], valueLength);
			});
		});
		// Convert data to a worksheet
		const worksheet = XLSX.utils.json_to_sheet(data, {
			header: Object.keys(headers),
			skipHeader: false,
		});
		// Set the widths for each column
		worksheet["!cols"] = Object.keys(maxWidths).map((key) => ({
			wch: maxWidths[key] + 2 // +2 for a little extra padding
		}));
		const workbook = XLSX.utils.book_new();
		XLSX.utils.book_append_sheet(workbook, worksheet, "Data"); 
		// Export the workbook to a .xlsx file
		XLSX.writeFile(workbook, "Tours.xlsx");
	} catch (error) {
		console.error("Error downloading data: ", error);
	}
};

</script>

<style>
body {
	background-color: #2a3b63;
}

.logo {
	padding: 10%;
	height: 3em;
}

h1 {
	text-align: center;
	color: #4caf50;
	font-size: 18px;
}

h2 {
	color: white;
	font-size: 16px;
}

.english {
	color: cyan;
}

.container {
	background-color: #2a3b63;
	color: white;
	padding: 5% 0;
	width: 75%;
	margin: auto;
}

.btn-next {
	width: 100%;
	background-color: green;
	color: white;
	padding: 20px 20px;
	margin-top: 20%;
	border: none;
	border-radius: 5px;
	cursor: pointer;
}


.btn-fin {
	width: 100%;
	background-color: #4c4faf;
	color: white;
	padding: 20px 20px;
	margin-top: 5%;
	border: none;
	border-radius: 5px;
	cursor: pointer;
}

.btn-return {
	width: 100%;
	background-color: #898989;
	color: white;
	padding: 20px 20px;
	margin-top: 5%;
	border: none;
	border-radius: 5px;
	cursor: pointer;
}

.btn-return:hover {
	background-color: #839684;
}

.commune-dropdown {
	/* Style your dropdown list here */
	list-style-type: none;
	padding: 0;
	margin: 0;
	border: 1px solid #ccc;
	border-radius: 4px;
	max-height: 200px;
	overflow-y: auto;
}

.form-control {
	width: 100%;
	border-radius: 5px;
	border: 1px solid white;
	background-color: #333;
	color: white;
	text-transform: uppercase;
	font-weight: bolder;
}

input.form-control {
	width: 93%;
}

.commune-dropdown li {
	padding: 5px 10px;
	cursor: pointer;
}

*:focus {
	outline: none;
}

.commune-dropdown li:hover {
	background-color: #f0f0f0;
}

input,
select,
button {
	font-size: 16px;
	padding: 10px;
}
</style>
