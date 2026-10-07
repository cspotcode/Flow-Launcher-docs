## SettingsTemplate.yaml editor

The editor below doesn't save your data: it's lost when you close the browser. To keep your work, press "Generate SettingsTemplate.yaml" and save the result.

To edit an existing SettingsTemplate.yaml file here, paste its contents on this page.

<settings-generator></settings-generator>

<script>
const element = document.querySelector('#__settings-script__');
if (!element) {
    const script = document.createElement('script');
    script.id = '__settings-script__';
    script.src = 'https://www.flowlauncher.com/docs/webcomponents/dist/flow-launcher-docs-web-components.js';
    script.type = 'module';
    document.body.appendChild(script);
}
</script>
