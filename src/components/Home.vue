<template>
  <div id="home">

    <Card :bordered="false">
      <p slot="title">ProLease SAML Service Provider File Generator</p>
      <Form ref="formValidate" :model="formValidate" :rules="ruleValidate" :label-width="120">
        <FormItem label="Account Directory" prop="accountDirectory">
          <Input v-model="formValidate.accountDirectory" placeholder="eg. PL_XYZCompany" />
        </FormItem>
        <FormItem label="Expiration Date" prop="expirationDate">
          <Row type="flex" justify="start">
            <DatePicker v-model="formValidate.expirationDate" type="date" placeholder="eg. 2020-12-31"></DatePicker>
          </Row>
        </FormItem>
        <FormItem label="Logout URL" prop="logoutUrl">
          <Input v-model="formValidate.logoutUrl" placeholder="eg. http://www.mycompany.com/saml/logout.html" />
        </FormItem>
        <FormItem label="X509 Certificate" prop="certificate">
          <Input v-model="formValidate.certificate" type="textarea" :rows="20" />
        </FormItem>
        <FormItem>
          <Button type="primary" @click="handleSubmit('formValidate')">Generate SAML</Button>
          <Button type="ghost" @click="handleReset('formValidate')" style="margin-left: 8px">Clear All</Button>
        </FormItem>
      </Form>
    </Card>

  </div>
</template>

<script>
  import fileSaver from 'file-saver';
  import moment from 'moment';
  import samlTemplate from '../assets/saml.txt';

  export default {
    name: 'home',
    data() {
      return {
        formValidate: {
          accountDirectory: '',
          expirationDate: moment().add(5, 'years').toDate(),
          logoutUrl: '',
          certificate: '',
        },
        ruleValidate: {
          accountDirectory: [
            { required: true, message: 'Account Directory cannot be blank', trigger: 'blur' },
          ],
          expirationDate: [
            { required: true, type: 'date', message: 'Expiration Date cannot be blank', trigger: 'change' },
          ],
          certificate: [
            { required: true, message: 'X509 Certificate cannot be blank', trigger: 'blur' },
          ],
        },
      };
    },
    methods: {
      handleSubmit(name) {
        this.$refs[name].validate((valid) => {
          if (valid) {
            let saml = samlTemplate.replace(/##accountDirectory##/g, this.formValidate.accountDirectory);
            saml = saml.replace('##expirationDate##', this.formValidate.expirationDate);
            saml = saml.replace('##certificate##', this.formValidate.certificate);
            let logoutXml = '';
            if (this.formValidate.logoutUrl) {
              // added \n\t so that it will align with other tags
              logoutXml = `\n\t<md:SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="${this.formValidate.logoutUrl}"/>`;
            }
            saml = saml.replace('##logoutUrl##', logoutXml);
            const blob = new Blob([saml], { type: 'text/xml;charset=utf-8' });
            fileSaver.saveAs(blob, 'SAML-Service-Provider-Metadata.xml');
            this.$Message.success('Generated SAML Service Provider file!');
          } else {
            this.$Message.error('Please make sure to fill all required fields!');
          }
        });
      },
      handleReset(name) {
        this.$refs[name].resetFields();
      },
    },
    components: {
      fileSaver,
      moment,
    },
  };
</script>

<style lang="scss" scoped>
  #home{
    width: 700px;
  }

</style>
