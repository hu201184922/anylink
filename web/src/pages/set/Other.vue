<template>
  <el-card>
    <el-tabs v-model="activeName" @tab-click="handleClick">
      <el-tab-pane label="邮件配置" name="dataSmtp">
        <el-form
          :model="dataSmtp"
          ref="dataSmtp"
          :rules="rules"
          label-width="100px"
          class="tab-one"
        >
          <el-form-item label="服务器地址" prop="host">
            <el-input v-model="dataSmtp.host"></el-input>
          </el-form-item>
          <el-form-item label="服务器端口" prop="port">
            <el-input v-model.number="dataSmtp.port"></el-input>
          </el-form-item>
          <el-form-item label="用户名" prop="username">
            <el-input v-model="dataSmtp.username"></el-input>
          </el-form-item>
          <el-form-item label="密码" prop="password">
            <el-input
              type="password"
              v-model="dataSmtp.password"
              placeholder="密码为空则不修改"
            ></el-input>
          </el-form-item>
          <el-form-item label="加密类型" prop="encryption">
            <el-radio-group v-model="dataSmtp.encryption">
              <el-radio label="None">None</el-radio>
              <el-radio label="SSLTLS">SSLTLS</el-radio>
              <el-radio label="STARTTLS">STARTTLS</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="邮件from" prop="from">
            <el-input v-model="dataSmtp.from"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="submitForm('dataSmtp')"
              >保存</el-button
            >
            <el-button @click="resetForm('dataSmtp')">重置</el-button>
          </el-form-item>
        </el-form>
      </el-tab-pane>

      <el-tab-pane label="审计日志" name="dataAuditLog">
        <el-form
          :model="dataAuditLog"
          ref="dataAuditLog"
          :rules="rules"
          label-width="100px"
          class="tab-one"
        >
          <el-form-item label="审计去重间隔" prop="audit_interval">
            <el-input-number
              v-model="dataAuditLog.audit_interval"
              :min="-1"
              size="small"
              label="秒"
              :disabled="true"
            ></el-input-number>
            秒
            <p class="input_tip">
              请手动修改配置文件中的 audit_interval 参数后，再重启服务,
              <strong style="color: #ea3323">-1 代表关闭审计日志</strong>
            </p>
          </el-form-item>
          <el-form-item label="存储时长" prop="life_day">
            <el-input-number
              v-model="dataAuditLog.life_day"
              :min="0"
              :max="365"
              size="small"
              label="天数"
            ></el-input-number>
            天
            <p class="input_tip">
              范围: 0 ~ 365天 ,
              <strong style="color: #ea3323">0 代表永久保存</strong>
            </p>
          </el-form-item>
          <el-form-item label="清理时间" prop="clear_time">
            <el-time-select
              v-model="dataAuditLog.clear_time"
              :picker-options="{
                start: '00:00',
                step: '01:00',
                end: '23:00',
              }"
              editable="false,"
              size="small"
              placeholder="请选择"
              style="width: 130px"
            >
            </el-time-select>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="submitForm('dataAuditLog')"
              >保存</el-button
            >
            <el-button @click="resetForm('dataAuditLog')">重置</el-button>
          </el-form-item>
        </el-form>
      </el-tab-pane>
      <el-tab-pane label="证书设置" name="datacertManage">
        <el-tabs
          tab-position="left"
          v-model="datacertManage"
          @tab-click="handleClick"
        >
          <el-tab-pane label="自定义证书" name="customCert">
            <el-form
              ref="customCert"
              :model="customCert"
              label-width="100px"
              size="small"
              class="tab-one"
            >
              <el-form-item>
                <el-upload
                  class="uploadCert"
                  :before-upload="beforeCertUpload"
                  :action="certUpload"
                  :limit="1"
                >
                  <el-button size="mini" icon="el-icon-plus" slot="trigger"
                    >证书文件</el-button
                  >
                  <el-tooltip
                    class="item"
                    effect="dark"
                    content="请上传 .pem 格式的 cert 文件"
                    placement="top"
                  >
                    <i class="el-icon-info"></i>
                  </el-tooltip>
                </el-upload>
              </el-form-item>
              <el-form-item>
                <el-upload
                  class="uploadCert"
                  :before-upload="beforeKeyUpload"
                  :action="certUpload"
                  :limit="1"
                >
                  <el-button size="mini" icon="el-icon-plus" slot="trigger"
                    >私钥文件</el-button
                  >
                  <el-tooltip
                    class="item"
                    effect="dark"
                    content="请上传 .pem 格式的 key 文件"
                    placement="top"
                  >
                    <i class="el-icon-info"></i>
                  </el-tooltip>
                </el-upload>
              </el-form-item>
              <el-form-item>
                <el-button
                  size="small"
                  icon="el-icon-upload"
                  type="primary"
                  @click="submitForm('customCert')"
                  >上传</el-button
                >
              </el-form-item>
            </el-form>
          </el-tab-pane>
          <el-tab-pane label="Let's Encrypt证书" name="letsCert">
            <el-form
              :model="letsCert"
              ref="letsCert"
              :rules="rules"
              label-width="120px"
              size="small"
              class="tab-one"
            >
              <el-form-item label="域名" prop="domain">
                <el-input v-model="letsCert.domain"></el-input>
              </el-form-item>
              <el-form-item label="邮箱" prop="legomail">
                <el-input v-model="letsCert.legomail"></el-input>
              </el-form-item>
              <el-form-item label="域名服务商" prop="name">
                <el-radio-group v-model="letsCert.name">
                  <el-radio label="aliyun">阿里云</el-radio>
                  <el-radio label="txcloud">腾讯云</el-radio>
                  <el-radio label="cfcloud">cloudflare</el-radio>
                </el-radio-group>
              </el-form-item>
              <el-form-item
                v-for="component in dnsProvider[letsCert.name]"
                :key="component.prop"
                :label="component.label"
                :rules="component.rules"
              >
                <component
                  :is="component.component"
                  :type="component.type"
                  v-model="letsCert[letsCert.name][component.prop]"
                ></component>
              </el-form-item>
              <el-form-item>
                <el-switch
                  style="display: block"
                  v-model="letsCert.renew"
                  active-color="#13ce66"
                  inactive-color="#ff4949"
                  inactive-text="自动续期"
                >
                </el-switch>
              </el-form-item>
              <el-form-item>
                <el-button type="primary" @click="submitForm('letsCert')"
                  >申请</el-button
                >
                <el-button @click="resetForm('letsCert')">重置</el-button>
              </el-form-item>
            </el-form>
          </el-tab-pane>
        </el-tabs>
      </el-tab-pane>
      <el-tab-pane label="其他设置" name="dataOther">
        <el-form
          :model="dataOther"
          ref="dataOther"
          :rules="rules"
          label-width="100px"
          class="tab-one"
        >
          <el-form-item label="vpn对外地址" prop="link_addr">
            <el-input placeholder="请输入内容" v-model="dataOther.link_addr">
            </el-input>
          </el-form-item>

          <el-form-item label="Banner信息" prop="banner">
            <el-input
              type="textarea"
              :rows="5"
              placeholder="请输入内容"
              v-model="dataOther.banner"
            >
            </el-input>
          </el-form-item>

          <el-form-item label="自定义首页" prop="homeindex">
            <el-input
              type="textarea"
              :rows="10"
              placeholder="请输入内容"
              v-model="dataOther.homeindex"
            >
            </el-input>
            <el-tooltip content="自定义内容可以参考 home 目录下的文件" placement="top">
              <i class="el-icon-question"></i>
            </el-tooltip>
          </el-form-item>

          <el-form-item label="账户开通邮件" prop="account_mail">
            <el-input
              type="textarea"
              :rows="10"
              placeholder="请输入内容"
              v-model="dataOther.account_mail"
            >
            </el-input>
          </el-form-item>

          <el-form-item label="邮件展示">
            <iframe
              width="500px"
              height="300px"
              :srcdoc="dataOther.account_mail"
            >
            </iframe>
          </el-form-item>

          <el-form-item>
            <el-button type="primary" @click="submitForm('dataOther')"
              >保存</el-button
            >
            <el-button @click="resetForm('dataOther')">重置</el-button>
          </el-form-item>
        </el-form>
      </el-tab-pane>
    </el-tabs>
  </el-card>
</template>

<script>
import axios from "axios";

export default {
  name: "Other",
  created() {
    this.$emit("update:route_path", this.$route.path);
    this.$emit("update:route_name", ["基础信息", "其他设置"]);
  },
  mounted() {
    this.getSmtp();
  },
  data() {
    return {
      activeName: "dataSmtp",
      datacertManage: "customCert",
      dataSmtp: {},
      dataAuditLog: {},
      letsCert: {
        domain: ``,
        legomail: ``,
        name: "",
        renew: "",
        aliyun: {
          apiKey: "",
          secretKey: "",
        },
        txcloud: {
          secretId: "",
          secretKey: "",
        },
        cfcloud: {
          authToken: "",
        },
      },
      customCert: { cert: "", key: "" },
      dataOther: {},
      rules: {
        host: { required: true, message: "请输入服务器地址", trigger: "blur" },
        port: [
          { required: true, message: "请输入服务器端口", trigger: "blur" },
          {
            type: "number",
            message: "请输入正确的服务器端口",
            trigger: ["blur", "change"],
          },
        ],
        issuer: { required: true, message: "请输入系统名称", trigger: "blur" },
        domain: {
          required: true,
          message: "请输入需要申请证书的域名",
          trigger: "blur",
        },
        legomail: {
          required: true,
          message: "请输入申请证书的邮箱地址",
          trigger: "blur",
        },
        name: { required: true, message: "请选择域名服务商", trigger: "blur" },
      },
      certUpload: "/set/other/customcert",
      dnsProvider: {
        aliyun: [
          {
            label: "APIKey",
            prop: "apiKey",
            component: "el-input",
            type: "password",
            rules: {
              required: true,
              message: "请输入正确的APIKey",
              trigger: "blur",
            },
          },
          {
            label: "SecretKey",
            prop: "secretKey",
            component: "el-input",
            type: "password",
            rules: {
              required: true,
              message: "请输入正确的SecretKey",
              trigger: "blur",
            },
          },
        ],
        txcloud: [
          {
            label: "SecretID",
            prop: "secretId",
            component: "el-input",
            type: "password",
            rules: {
              required: true,
              message: "请输入正确的APIKey",
              trigger: "blur",
            },
          },
          {
            label: "SecretKey",
            prop: "secretKey",
            component: "el-input",
            type: "password",
            rules: {
              required: true,
              message: "请输入正确的APIKey",
              trigger: "blur",
            },
          },
        ],
        cfcloud: [
          {
            label: "AuthToken",
            prop: "authToken",
            component: "el-input",
            type: "password",
            rules: {
              required: true,
              message: "请输入正确的AuthToken",
              trigger: "blur",
            },
          },
        ],
      },
    };
  },
  methods: {
    handleClick(tab, event) {
      window.console.log(tab.name, event);
      switch (tab.name) {
        case "dataSmtp":
          this.getSmtp();
          break;
        case "dataAuditLog":
          this.getAuditLog();
          break;
        case "letsCert":
          this.getletsCert();
          break;
        case "dataOther":
          this.getOther();
          break;
      }
    },
    beforeCertUpload(file) {
      // if (file.type !== 'application/x-pem-file') {
      //   this.$message.error('只能上传 .pem 格式的证书文件')
      //   return false
      // }
      this.customCert.cert = file;
    },
    beforeKeyUpload(file) {
      // if (file.type !== 'application/x-pem-file') {
      //   this.$message.error('只能上传 .pem 格式的私钥文件')
      //   return false
      // }
      this.customCert.key = file;
    },
    getSmtp() {
      axios
        .get("/set/other/smtp")
        .then((resp) => {
          let rdata = resp.data;
          console.log(rdata);
          if (rdata.code !== 0) {
            this.$message.error(rdata.msg);
            return;
          }
          this.dataSmtp = rdata.data;
        })
        .catch((error) => {
          this.$message.error("哦，请求出错");
          console.log(error);
        });
    },
    getAuditLog() {
      axios
        .get("/set/other/audit_log")
        .then((resp) => {
          let rdata = resp.data;
          console.log(rdata);
          if (rdata.code !== 0) {
            this.$message.error(rdata.msg);
            return;
          }
          this.dataAuditLog = rdata.data;
        })
        .catch((error) => {
          this.$message.error("哦，请求出错");
          console.log(error);
        });
    },
    getletsCert() {
      axios
        .get("/set/other/getcertset")
        .then((resp) => {
          let rdata = resp.data;
          console.log(rdata);
          if (rdata.code !== 0) {
            this.$message.error(rdata.msg);
            return;
          }
          this.letsCert = Object.assign({}, this.letsCert, rdata.data);
        })
        .catch((error) => {
          this.$message.error("哦，请求出错");
          console.log(error);
        });
    },
    getOther() {
      axios
        .get("/set/other")
        .then((resp) => {
          let rdata = resp.data;
          console.log(rdata);
          if (rdata.code !== 0) {
            this.$message.error(rdata.msg);
            return;
          }
          this.dataOther = rdata.data;
        })
        .catch((error) => {
          this.$message.error("哦，请求出错");
          console.log(error);
        });
    },
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (!valid) {
          alert("error submit!");
        }

        switch (formName) {
          case "dataSmtp":
            axios.post("/set/other/smtp/edit", this.dataSmtp).then((resp) => {
              var rdata = resp.data;
              console.log(rdata);
              if (rdata.code === 0) {
                this.$message.success(rdata.msg);
              } else {
                this.$message.error(rdata.msg);
              }
            });
            break;
          case "dataAuditLog":
            axios
              .post("/set/other/audit_log/edit", this.dataAuditLog)
              .then((resp) => {
                var rdata = resp.data;
                console.log(rdata);
                if (rdata.code === 0) {
                  this.$message.success(rdata.msg);
                } else {
                  this.$message.error(rdata.msg);
                }
              });
            break;
          case "letsCert":
            var loading = this.$loading({
              lock: true,
              text: "证书申请中...",
              spinner: "el-icon-loading",
              background: "rgba(0, 0, 0, 0.7)",
            });
            axios.post("/set/other/createcert", this.letsCert).then((resp) => {
              var rdata = resp.data;
              console.log(rdata);
              if (rdata.code === 0) {
                loading.close();
                this.$message.success(rdata.msg);
              } else {
                loading.close();
                this.$message.error(rdata.msg);
              }
            });
            break;
          case "customCert":
            var formData = new FormData();
            formData.append("cert", this.customCert.cert);
            formData.append("key", this.customCert.key);
            axios.post(this.certUpload, formData).then((resp) => {
              var rdata = resp.data;
              console.log(rdata);
              if (rdata.code === 0) {
                this.$message.success(rdata.msg);
              } else {
                this.$message.error(rdata.msg);
              }
            });
            break;
          case "dataOther":
            axios.post("/set/other/edit", this.dataOther).then((resp) => {
              var rdata = resp.data;
              console.log(rdata);
              if (rdata.code === 0) {
                this.$message.success(rdata.msg);
              } else {
                this.$message.error(rdata.msg);
              }
            });
            break;
        }
      });
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    },
  },
};
</script>

<style scoped>
.tab-one {
  width: 700px;
}

.input_tip {
  line-height: 1.428;
  margin: 2px 0 0 0;
}
</style>
