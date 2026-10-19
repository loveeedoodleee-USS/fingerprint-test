# fingerprint-test
<!DOCTYPE html>
<html>
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Fingerprint Test</title>
</head>
<body style="font-family: sans-serif; padding: 20px;">
  <h2>Fingerprint Test</h2>
  <p id="iframe-result"></p>
  <p id="finger-result">Check ho raha hai...</p>

  <button onclick="testCreate()" style="font-size:18px; padding:10px;">
    Fingerprint register test
  </button>
  <p id="out"></p>

  <script>
    document.getElementById('iframe-result').innerText =
      (window.self !== window.top)
        ? 'Iframe: HAAN'
        : 'Iframe: NAHI (seedha khula hai)';

    if (window.PublicKeyCredential) {
      PublicKeyCredential.isUserVerifyingPlatformAuthenticatorAvailable()
        .then(function (ok) {
          document.getElementById('finger-result').innerText =
            'Fingerprint support: ' + ok;
        })
        .catch(function (e) {
          document.getElementById('finger-result').innerText =
            'Error: ' + e.name;
        });
    } else {
      document.getElementById('finger-result').innerText =
        'Browser support nahi';
    }

    async function testCreate() {
      const out = document.getElementById('out');
      out.innerText = 'Chal raha hai...';
      try {
        await navigator.credentials.create({
          publicKey: {
            challenge: crypto.getRandomValues(new Uint8Array(32)),
            rp: { name: 'Test' },
            user: {
              id: crypto.getRandomValues(new Uint8Array(16)),
              name: 'test@test.com',
              displayName: 'Test'
            },
            pubKeyCredParams: [
              { type: 'public-key', alg: -7 },
              { type: 'public-key', alg: -257 }
            ],
            authenticatorSelection: {
              authenticatorAttachment: 'platform',
              userVerification: 'required'
            },
            timeout: 60000
          }
        });
        out.innerText = 'SUCCESS: fingerprint chal gaya';
      } catch (e) {
        out.innerText = 'ERROR: ' + e.name + ' - ' + e.message;
      }
    }
  </script>
</body>
</html>
