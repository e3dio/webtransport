# webtransport client

```javascript
const read = async (stream) => {
    for await (const chunk of stream) {
        console.log('stream chunk:', chunk);
    }
};

const listenUni = async (wt) => {
    for await (const stream of wt.incomingUnidirectionalStreams) {
        read(stream);
    }
};

const done = c => console.log('WebTransport closed normally');
const error = e => console.log(`WebTransport closed abnormally with code: ${e.closeCode}, reason: ${e.reason}`);
const connect = () => {
    const wt = new WebTransport('https://domain.com:3000/path');
    listenUni(wt);
    wt.closed.then(done, error).then(() => setTimeout(connect, 2000));
};

connect();
```
