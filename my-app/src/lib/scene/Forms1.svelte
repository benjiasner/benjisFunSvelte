<script>
    let name = '';
    let email = '';
    let subject = '';
    let message = '';

    // HOW TO: https://github.com/levinunnink/html-form-to-google-sheet

    // Web app URL
    const scriptURL = "https://script.google.com/macros/s/AKfycbxQ0TvOE375NIuuO1w0T6tnJBHYiN1aC9OaPEA5XfoElmu6s1clrokVBGjKNA5j1w8j/exec";

    const handleSubmit = async (event) => {
        event.preventDefault(); // Prevent the default form submission (page reload)

        const formData = new FormData(event.target);

        try {
            const response = await fetch(scriptURL, {
                method: 'POST',
                body: formData
            });

            const data = await response.json();
            console.log('Success:', data);
            alert('Form submitted successfully!'); // Optional: Show a success message
            event.target.reset(); // Optional: Reset the form
        } catch (error) {
            console.error('Error:', error);
            alert('There was an error submitting your form.');
        }
    };
</script>

<form on:submit={handleSubmit}>
    <input name="Name" type="text" placeholder="Name" bind:value={name} required>
    <input name="Email" type="email" placeholder="Email" bind:value={email} required>
    <input name="Subject" type="text" placeholder="Subject" bind:value={subject}>
    <input name="Message" type="text" placeholder="Message" bind:value={message}>
    <button type="submit">Submit</button>
</form>
