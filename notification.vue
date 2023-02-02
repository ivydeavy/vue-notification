<template>
    <div>
        <div ref="notification" id="notification">
            <h3>{{ notificationHeading }}</h3>
            <p>{{ notificationMessage }}</p>
            <div ref="notificationLoader" id="notification-loader"></div>
        </div>
    </div>
</template>

<script>
export default {
    props: ["duration", "holdTimer", "heading", "content", "backgroundColor", "loaderColor", "place", "iteration", "textColor", "font"],
    computed: {
        notificationHeading() {
            return (this.heading || "Heading");
        },
        notificationMessage() {
            return (this.content || "Message");
        },
        timesIterate() {
            return (this.iteration || 1);
        },
    },
    mounted() {
        let notificationElement = this.$refs.notification;
        let notificationLoadingElement = this.$refs.notificationLoader;
        let placing = (this.place || "top-left");
        notificationElement.style.cssText = `background-color: ${this.backgroundColor || "#41af7f"}; border-radius: ${placing === "top-left" || placing === "bottom-left" ? "0 10px 10px 0" : "10px 0 0 10px"}; color: ${this.textColor || "white"}; font-family: ${this.font || "'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;"}`;
        notificationLoadingElement.style.cssText = `background-color: ${this.loaderColor || "#2c9c6c"};`;


        if (placing === "bottom-left" || "bottom-right") {
            notificationElement.style.cssText.concat("position: absolute; bottom: 0;");
        }
        else {
            notificationElement.style.cssText.concat("position: absolute; top: 0;");
        }
        const holdNotification = (this.holdTimer || 5000);
        const duration = (this.duration || 2000);
        function place() {
            switch (placing) {
                case "top-left": return { translateFrom: "-30vw", translateTo: "0vw" }; break;
                case "top-right": return { translateFrom: "130vw", translateTo: "80vw" }; break;
                case "bottom-left": return { translateFrom: "-30vw", translateTo: "0vw" }; break;
                case "bottom-right": return { translateFrom: "130vw", translateTo: "80vw" }; break;
                default: return { translateFrom: "-30vw", translateTo: "0vw" }; break;
            }
        }

        const keyframes = [
            { transform: `translateX(${place().translateFrom})` },
            { transform: `translateX(${place().translateTo})` },
        ];
        const inOptions = {
            duration: duration,
            iterations: 1,
            easing: "ease-out",
            fill: "forwards",
            direction: "alternate",
        };
        const outOptions = {
            duration: duration,
            iterations: 1,
            easing: "ease-out",
            fill: "forwards",
            direction: "reverse",
        };

        const loaderKeyframes = [
            { width: "0" },
            { width: "100%" }
        ];

        const loaderOptions = {
            duration: holdNotification,
            iterations: 1,
        }
        let i = 1;
        function mainCall(iterate){
            if(i <= iterate){
                const callNotification = new Promise((resolve, reject) => {
                        callNotificationLoader()
                        notificationElement.animate(keyframes, inOptions);
                        const completeIterate = new Promise((resolveIterate, rejectIterate) => {
                            setTimeout(() => {notificationElement.animate(keyframes, outOptions); setTimeout(() => {resolveIterate();}, (holdNotification/2))}, holdNotification);
                        })
                        completeIterate.then(() => resolve());
                })
                    callNotification.then(() => {
                        i++
                        mainCall(iterate)
                    })
            }
        }

        mainCall(this.timesIterate);

        function callNotificationLoader() {
            notificationLoadingElement.animate(loaderKeyframes, loaderOptions)
        }

    }
}
</script>

<style scoped>
#notification {
    margin: 10px 0 10px 0;
    width: 300px;
    padding: 5px 10px 5px 50px;
}

#notification-loader {
    border-radius: 10px;
    height: 10px;
}
</style>