
<script lang="ts" setup>

const iframe = ref<HTMLIFrameElement>()

async function startDevServer() {
    const wc = await useWebContainer();

    console.log("mounting!!!")
    await wc.mount({
        'package.json': {
            file: {
                contents: JSON.stringify({
                    private: true,
                    scripts: {
                        dev: 'nuxt dev'
                    },
                    dependencies: {
                        'nuxt': 'latest',
                    }
                }, null, 2)
            }
        }
    });

    wc.on('server-ready', (port, url) => {
        iframe.value!.src = url;
    });

    console.log("Installing...")
    const installProcess = await wc.spawn('npm', ['install']);

    const installExitCode = await installProcess.exit;


    if (installExitCode !== 0) {
        console.log("Unable to run npm install");
        throw new Error('Unable to run npm install');
    }

    console.log("Running...")

    // `npm run dev`
    await wc.spawn('npm', ['run', 'dev']);
}

onMounted(startDevServer);
</script>

<template>
    <div>

        <iframe ref="iframe" />
    </div>
</template>
