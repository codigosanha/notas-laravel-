<template>
   	<div>
	    <div class="card">
		   	<div class="card-header">
		    	Periodos
		  	</div>
		  	<div class="card-body">
		  		<button type="button" class="btn btn-primary float-right mb-3" data-toggle="modal" data-target="#exampleModal">
				  	Agregar Periodo
				</button>
				<b-button v-b-modal.modal-prevent-closing>Launch demo modal</b-button>
	
				<table class="table table-bordered">
					<thead>
						<th>Año</th>
						<th>Numero</th>
						<th>Periodo Actual</th>
						<th>Acciones</th>
					</thead>
					<tbody>
						<tr v-for="periodo in periodos">
							<td>{{ periodo.anio}}</td>
							<td>{{ periodo.numero}}</td>
							<td>
								<span class="badge badge-success" v-if="periodo.estado"> SI </span>
								<span class="badge badge-danger" v-else="periodo.estado"> NO </span>
							</td>
							<td>{{ periodo.anio}}</td>
						</tr>
					</tbody>
				</table>
		    	
		  	</div>
		</div>
		<div class="modal fade" id="exampleModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
		  	<div class="modal-dialog" role="document">
		    	<div class="modal-content">
		      		<div class="modal-header">
		        		<h5 class="modal-title" id="exampleModalLabel" v-text="title_modal"></h5>
		        		<button type="button" class="close" data-dismiss="modal" aria-label="Close">
		          			<span aria-hidden="true">&times;</span>
		        		</button>
		      		</div>
		      		<form @submit.prevent="savePeriodo">
			      	<div class="modal-body">
			      		
			        	<div class="form-group">
			        		<label for="">Año:</label>
							<datepicker :language="es" :format="DatePickerFormat" minimum-view="year" :bootstrap-styling="true"  v-model="defaultDate" @closed="convertDate"></datepicker>
			        	</div>
			        	<div class="form-group">
			        		<label for="">Número:</label>
							<select v-model="numero" id="numero" class="form-control" required="required">
								<option value="I">I</option>
								<option value="II">II</option>
							</select>
			        	</div>
			        	<div class="form-group">
			        		<label class="checkbox-inline"><input type="checkbox" v-model="periodo_actual" :true-value="true"
  							:false-value="false">Marcar como Activo</label>
			        	</div>
			      	</div>
			      	<div class="modal-footer">
			        	<button type="button" class="btn btn-secondary" data-dismiss="modal">Cerrar</button>
			        	<button type="submit" class="btn btn-primary">Guardar</button>
			      	</div>
			      	</form>
		    	</div>
		  	</div>
		</div>
		<b-modal
	      	id="modal-prevent-closing"
	      	ref="modal"
	      	title="Submit Your Name"
	      	@show="resetModal"
	      	@hidden="resetModal"
	      	@ok="handleOk"
	      	hide-footer
	    >
	      	 <b-form @submit.stop.prevent="onSubmit">
      <b-form-group id="example-input-group-1" label="Name" label-for="example-input-1">
        <b-form-input
          id="example-input-1"
          name="example-input-1"
          v-model="$v.form.name.$model"
          :state="validateState('name')"
          aria-describedby="input-1-live-feedback"
        ></b-form-input>

        <b-form-invalid-feedback
          id="input-1-live-feedback"
        >This is a required field and must be at least 3 characters.</b-form-invalid-feedback>
      </b-form-group>

      <b-form-group id="example-input-group-2" label="Food" label-for="example-input-2">
        <b-form-select
          id="example-input-2"
          name="example-input-2"
          v-model="$v.form.food.$model"
          :options="foods"
          :state="validateState('food')"
          aria-describedby="input-2-live-feedback"
        ></b-form-select>

        <b-form-invalid-feedback id="input-2-live-feedback">This is a required field.</b-form-invalid-feedback>
      </b-form-group>

      <b-button type="submit" variant="primary">Submit</b-button>
      <b-button class="ml-2" @click="resetForm()">Reset</b-button>
    </b-form>
	    </b-modal>
	</div>
</template>

<script>

	import { es } from 'vuejs-datepicker/dist/locale';
	import Datepicker from 'vuejs-datepicker';
	import { validationMixin } from "vuelidate";
	import { required, minLength } from "vuelidate/lib/validators";


	export default {
		mixins: [validationMixin],
		data(){
			return {
				DatePickerFormat: 'yyyy',
				es: es,
				title_modal: 'Registrar Periodo',
				periodos:[],
				anio:'',
				numero:null,
				defaultDate: new Date(),
				periodo_actual: true,
				name: '',
        		nameState: null,
        		submittedNames: [],
        		numeros: [
		          { value: null, text: 'Please select an option' },
		          { value: 'I', text: 'I' },
		          { value: 'II', text: 'II' },
		        ],
		        foods: [
			        { value: null, text: "Choose..." },
			        { value: "apple", text: "Apple" },
			        { value: "orange", text: "Orange" }
			    ],
			    form: {
			        name: null,
			        food: null
			    }

			}
		},

		validations: {
		    form: {
		      food: {
		        required
		      },
		      name: {
		        required,
		        minLength: minLength(3)
		      }
		    }
		  },
		methods:{
			getPeriodos: function(){
				axios.get('/periodos')
					.then(resp=>{
						this.periodos = resp.data;
					}).catch(error=>{
						console.log(error);
					});
			},
			convertDate: function () {
		      	// `this` points to the vm instance
		      	this.anio =  this.defaultDate.getFullYear();
		    },
		    savePeriodo(){
		    	axios.post('/periodos',{
		    		periodo_actual: this.periodo_actual,
		    		anio: this.anio,
		    		numero: this.numero,
	    		})
				.then(resp=>{
					console.log(resp);
				}).catch(error=>{
					console.log(error);
				});
		    },
		     checkFormValidity() {
		        const valid = this.$refs.form.checkValidity()
		        this.nameState = valid
		        return valid
		      },
		      resetModal() {
		        this.name = ''
		        this.nameState = null
		      },
		      handleOk(bvModalEvt) {
		        // Prevent modal from closing
		        bvModalEvt.preventDefault()
		        // Trigger submit handler
		        this.handleSubmit()
		      },
		      handleSubmit() {
		        // Exit when the form isn't valid
		        if (!this.checkFormValidity()) {
		          return
		        }
		        // Push the name to submitted names
		        this.submittedNames.push(this.name)
		        // Hide the modal manually
		        this.$nextTick(() => {
		          this.$bvModal.hide('modal-prevent-closing')
		        })
		      },
		       validateState(name) {
			      const { $dirty, $error } = this.$v.form[name];
			      return $dirty ? !$error : null;
			    },
			    resetForm() {
			      this.form = {
			        name: null,
			        food: null
			      };

			      this.$nextTick(() => {
			        this.$v.$reset();
			      });
			    },
			    onSubmit() {
			      this.$v.form.$touch();
			      if (this.$v.form.$anyError) {
			        return;
			      }
			      alert("Form submitted!");
			  }

			
		},
	
		mounted(){
			this.getPeriodos();
			this.convertDate();
		},
		components: {
		    Datepicker
		}
	}
</script>
<style>
	.vdp-datepicker .input-group .form-control[readonly] {
background: none !important;
}
</style>