<template>
  <el-container class="is-vertical page">
    <div class="box">
      <h2 class="title">UrbanKit</h2>
      <p class="slogan">
        /*
        <span class="typed"></span>
        */
      </p>
      <div class="button-box">
        <el-button type="info" class="button2" @click="refreshDataList();getStartVisible=true">Get Started</el-button>
      </div>
    </div>
    <el-dialog title="Data" :visible.sync="getStartVisible" width="40%" center>
      <el-tabs style="height: 300px">
        <el-tab-pane label="Manage">
          <!-- View Data Begin -->
          <el-table :data="tableData" style="width: 100%" height="250">
            <el-table-column label="File">
              <template slot-scope="scope">
                <i class="el-icon-files"></i>
                <span style="margin-left: 10px">{{ scope.row.name }}</span>
              </template>
            </el-table-column>
            <el-table-column label="Operation" width="180px">
              <template slot-scope="scope">
                <el-button size="mini" @click="handleSelect(scope.$index, scope.row)">Select</el-button>
                <el-button size="mini" type="danger" @click="handleDelete(scope.$index, scope.row)">Delete</el-button>
              </template>
            </el-table-column>
          </el-table>
          <!-- View Data End -->
        </el-tab-pane>
        <el-tab-pane label="Upload">
          <el-upload multiple :limit="6" :on-exceed="handleExceed" :on-success="handleSuccess" :on-error="handleError" :before-remove="beforeRemove" ref="upload" :before-upload="beforeUpload" :action="upload_api" :file-list="fileList" :auto-upload="false" accept=".csv">
            <el-button slot="trigger" size="small" type="primary">Select File</el-button>
            <el-button style="margin-left: 10px;" size="small" type="success" @click="submitUpload">Submit</el-button>
          </el-upload>
        </el-tab-pane>
      </el-tabs>
    </el-dialog>
  </el-container>
</template>
<script>
export default {
  name: 'Home',
  data() {
    return {
      loading: {},
      fileList: [],
      getStartVisible: false,
      tableData: [],
      upload_api: "http://" + this.config_.backend + this.global_.apis["upload_data"],
    }
  },
  mounted: function() {
    var typed = new Typed('.typed', {
      strings: ["An <b>Out-Of-Box</b> Analysis Tool For Sensory Data.",
        "It's Simple But Strong, Enjoy It!"
      ],
      typeSpeed: 50,
      backSpeed: 20,
      loop: true,
    });
  },
  methods: {
    refreshDataList() {
      this.tableData = []
      var promise = this.GetDataList();
      promise.then((response) => {
        var data = response.data;
        for (var i = 0; i < data.length; ++i) {
          this.tableData.push({ name: data[i] });
        }
      })
    },
    submitUpload() {
      this.$refs.upload.submit();
      setTimeout(() => {
        this.refreshDataList();
      }, 1000);
    },
    beforeRemove(file, fileList) {
      return true;
    },
    beforeUpload(file) {
      this.loading[file.name] = this.$loading({
        lock: true,
        text: 'Uploading',
        spinner: 'el-icon-loading',
        background: 'rgba(0, 0, 0, 0.7)'
      });
      return true;
    },
    handleExceed(files, fileList) {
      this.$message.warning(`Filelist length shouldn't exceed 6, selected ${files.length + fileList.length} files now.`);
    },
    handleSuccess(response, file, fileList) {
      this.loading[file.name].close();
      this.$message({
        message: "File upload successfully!",
        type: "success"
      });
    },
    handleError(err, file, fileList) {
      this.loading.close();
      this.$message.error("Failed to upload file.")
    },
    handleSelect(index, row) {
      const loading = this.$loading({
        lock: true,
        text: 'Loading',
        spinner: 'el-icon-loading',
        background: 'rgba(0, 0, 0, 0.7)'
      });
      setTimeout(() => {
        loading.close();
        this.global_.data_name = row.name;
        this.$router.replace('/Analysis')
      }, 2000);
    },
    handleDelete(index, row) {
      var promise = this.DeleteData(row.name);
      promise.then((response) => {
        this.$message({
          message: "Data has been deleted!",
          type: "success"
        });
        this.refreshDataList();
      }).catch((error) => {
        this.$message.error("Failed to delete data!");
        console.log(error);
      })
    }
  },
}

</script>
<style scoped>
.page {
  background-image: url("../assets/home.png");
  background-size: 100% 100%;
  background-repeat: no-repeat;
  padding: 2 2 2 2;
  margin: 0 0 0 0;
  width: 100%;
  height: 100%;
}

.box {
  font-family: century gothic, texgyreadventor, stheiti, sans-serif;
  position: absolute;
  width: 100%;
  min-height: 200px;
  top: 25%;
}

.button-box {
  position: relative;
  top: 35px;
}

.title {
  font-size: 81px;
  font-weight: 500;
  line-height: 68px;
  margin: 0 0 34px;
  padding: 0 30px;
  text-align: center;
  color: #FFFFFFFF;
  letter-spacing: 3px;
}

.slogan {
  color: #C0C0C0FF;
  font-size: 15px;
  font-weight: 450;
  line-height: 55px;
  letter-spacing: 6px;
  text-transform: uppercase;
  text-align: center;
}

/*linear-gradient(to right, #67b26b, #4ca2cb);*/
.button1,
.button2 {
  font-family: century gothic, texgyreadventor, stheiti, sans-serif;
  opacity: 0.8;
  border-radius: 24px;
  background: white;
  border: 1px solid;
  color: #1C2C41;
  text-align: center;
  font-size: 16px;
  padding: 15px;
  padding-left: 30px;
  padding-right: 30px;
  margin: 1% 6% 1% 6%;
}

.button2:hover,
.button1:hover {
  /*background: white;*/
  /*opacity: 0.9;*/
  /*color: #404040;*/
  /*font-weight: 700;*/
  /*letter-spacing: 3px;*/
  border-color: white;
  background: #1C2C41;
  color: white;
  transition: all 0.3s ease 0s;
}

</style>
