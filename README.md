# WebTransport

Browser client code examples:

```javascript
// Helper functions

const listenGram = async (wt) => {
   for await (const data of wt.datagrams.readable) {
      console.log('datagram message:', data);
   }
};
const readStream = async (stream) => {
   for await (const chunk of stream) {
      console.log('stream chunk:', chunk);
   }
};
const listenUniStream = async (wt) => {
   for await (const stream of wt.incomingUnidirectionalStreams) {
      console.log('new uni stream');
      readStream(stream);
   }
};
const listenBiStream = async (wt) => {
   for await (const biStream of wt.incomingBidirectionalStreams) {
      console.log('new bi stream');
      readStream(biStream.readable);
      const writer = biStream.writable.getWriter();
      writer.write(data);
   }
};

// WebTransport setup

const connect = () => {
    const wt = new WebTransport('https://domain.com:3000/path');
    listenGram(wt); // listen incoming datagram messages
    listenUniStream(wt); // listen incoming uni streams
    listenBiStream(wt); // listen incoming bi streams
    wt.createUnidirectionalStream().then(stream => { // create uni stream
        const writer = stream.getWriter();
        writer.write(data);
    });
    wt.createBidirectionalStream().then(biStream => { // create bi stream
       readStream(biStream.readable);
       const writer = biStream.writable.getWriter();
       writer.write(data);
    });
    wt.closed.then(done, error).then(() => setTimeout(connect, 2000)); // auto re-connect after close
};
const done = c => console.log('WebTransport closed normally', c);
const error = e => console.log(`WebTransport closed abnormally with code: ${e.closeCode}, reason: ${e.reason}`, e);

connect();

```
