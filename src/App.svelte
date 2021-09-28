<script>
	let state = 'init';

    let x, y

    function generatexy(){
        x =  Math.floor(Math.random() * 9)+1
        y =  Math.floor(Math.random() * 9)+1
        recognition.stop();
        let utterance = new SpeechSynthesisUtterance(`${x} times ${y} ?`);
        speechSynthesis.speak(utterance); 
        utterance.onend = function(){
            //console.log('end');
            recognition.start();
        }          

    }
    
//var grammar = '#JSGF V1.0; grammar colors; public <color> = aqua | azure | beige | bisque | black | blue | brown | chocolate | coral | crimson | cyan | fuchsia | ghostwhite | gold | goldenrod | gray | green | indigo | ivory | khaki | lavender | lime | linen | magenta | maroon | moccasin | navy | olive | orange | orchid | peru | pink | plum | purple | red | salmon | sienna | silver | snow | tan | teal | thistle | tomato | turquoise | violet | white | yellow ;'
var recognition = new webkitSpeechRecognition();
//var speechRecognitionList = new webkitSpeechGrammarList();
//speechRecognitionList.addFromString(grammar, 1);
//recognition.grammars = speechRecognitionList;
recognition.continuous = true;
recognition.lang = 'de-DE'//'en-US';
recognition.interimResults = true;
recognition.maxAlternatives = 1;

//var diagnostic = document.querySelector('.output');
//var bg = document.querySelector('html');

let start = function() {
  recognition.start();
  state = 'go'
  console.log('Ready to receive command.');
  generatexy()
}

let final_transcript = ''
let interim_transcript = ''
recognition.onresult = function(event) {
    // Loop through the results from the speech recognition object.
for (let i = event.resultIndex; i < event.results.length; ++i) {
  // If the result item is Final, add it to Final Transcript, Else add it to Interim transcript
  final_transcript=''
  if (event.results[i].isFinal) {
    final_transcript = event.results[i][0].transcript;
  } else {
    interim_transcript = event.results[i][0].transcript;
  }

  if (!final_transcript) return 
  if (final_transcript.split(' ').includes(String(x*y))) {
    recognition.stop()
    let utterance = new SpeechSynthesisUtterance(`Correct`);
    utterance.onend = function(){
        final_transcript = ''
        generatexy()
    }    
    speechSynthesis.speak(utterance); 
  } else {
    recognition.stop()
    final_transcript = ''
    let utterance = new SpeechSynthesisUtterance(`Wrong!`);
    utterance.onend = function(){
        //console.log('end');
        recognition.start();
    }    
    speechSynthesis.speak(utterance); 
  }

}
    //console.log('onresult')
  //var color = event.results[0][0].transcript;
  //console.log(color)
  //diagnostic.textContent = 'Result received: ' + color;
  //bg.style.backgroundColor = color;
}	
	
</script>
<center>
{#if state == 'init' }
<button on:click="{start}">Start</button>
{/if}

{#if state == 'go' }
<h1>{x} * {y} = <input readonly style="width:9rem; display:inline" value={final_transcript} />
</h1>
<button on:click="{generatexy}">Skip</button>

<br>
<textarea tabindex="-1" style="width:80%; height:10rem; opacity:0.5" readonly>{interim_transcript}</textarea>

{/if}


<!--
<h3>final_transcript: {final_transcript}</h3>
<h3>interim_transcript: {interim_transcript}</h3>
-->
</center>