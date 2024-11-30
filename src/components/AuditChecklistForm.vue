<template>
    <div>
        Document Title: <input type="text" class="w-50" v-model="documentTitle">
    </div>
    <div class="mb-3">
        <label for="obj" class="form-label">Objective</label>
        <textarea class="form-control" id="obj" v-model="objective" rows="3"></textarea>
    </div>
    <div>
        <table class="table table-hover">
        <thead>
          <tr>
            <th class="w-5">No.</th>
            <th class="w-50">Description</th>
            <th class="w-5">Assertion</th>
            <th>Reference</th>
            <th>Yes/No/NA</th>
            <th>Remark</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in checklistItems" :key="index">
            <td>{{ index + 1 }}</td>
            <td><textarea class="form-control" rows="5" v-model="item.description"></textarea></td>
            <td><input type="text" v-model="item.assertion"></td>
            <td>
             <textarea v-model="item.reference"></textarea>
              <button @click="addHyperlink(index)">Add Link</button>
            </td>
            <td>
              <select v-model="item.status">
                <option value="yes">Yes</option>
                <option value="no">No</option>
                <option value="na">N/A</option>
              </select>
            </td>
            <td><textarea v-model="item.remark"></textarea></td>
            <td><button @click="removeItem(index)">Remove</button></td>
          </tr>
        </tbody>
      </table>
  
      <button @click="addItem">Add Item</button>
      <button @click="exportJson">Export as JSON</button>
      <input type="file" @change="importJson" accept=".work">
    </div>
    <div class="mb-3">
        <label for="conclu" class="form-label">Conclusion</label>
        <textarea class="form-control" id="conclu" v-model="conclusion" rows="3"></textarea>
    </div>
  </template>
  
  <script>
  export default {
    mounted() {
      // Import Bootstrap's JavaScript in the mounted() hook
      import('bootstrap/dist/js/bootstrap.js');
    },
    data() {
      return {
        objective: '',
        conclusion: '',
        documentTitle: '',
        checklistItems: [
          // Initial checklist items if needed
          { 
            description: '', 
            assertion: '', 
            reference: '', 
            status: '', 
            remark: ''
          }
        ],
      };
    },
    methods: {
      addItem() {
        this.checklistItems.push({ 
          description: '', 
          assertion: '', 
          reference: '', 
          status: '', 
          remark: ''
        });
      },
      removeItem(index) {
        this.checklistItems.splice(index, 1);
      },
      addHyperlink(index) {
        // Prompt user for hyperlink and add it to the reference field
        const link = prompt("Enter the hyperlink:");
        if (link) {
          this.checklistItems[index].reference = `<a href="${link}" target="_blank">${this.checklistItems[index].reference}</a>`;
        }
      },
      exportJson() {
        const data = {
          documentTitle: this.documentTitle,
          objective: this.objective,
          conclusion: this.conclusion,
          checklistItems: this.checklistItems
        };
        const jsonString = JSON.stringify(data);
        const link = document.createElement('a');
        link.href = 'data:application/json;charset=utf-8,' + encodeURIComponent(jsonString);
        link.download = 'audit_checklist.work';
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
      },
      importJson(event) {
        const file = event.target.files[0];
        const reader = new FileReader();
        reader.onload = (e) => {
          try {
            const data = JSON.parse(e.target.result);
            this.documentTitle = data.documentTitle;
            this.objective = data.objective;
            this.conclusion = data.conclusion;
            this.checklistItems = data.checklistItems;
          } catch (error) {
            alert("Error importing JSON file.");
          }
        };
        reader.readAsText(file);
      },
    },
  };
  </script>
  <style>
  /* Import Bootstrap's CSS */
  @import 'bootstrap/dist/css/bootstrap.css';
  </style>
  