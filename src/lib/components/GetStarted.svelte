<script lang="ts">
	import { onMount } from 'svelte';
	import gsap from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';
	import CodeSnippet from './CodeSnippet.svelte';
	import type { CodeTab } from '$lib/utils/shiki';

	gsap.registerPlugin(ScrollTrigger);

	let section: HTMLElement | undefined = $state();

	interface Step {
		number: number;
		title: string;
		tabs: CodeTab[];
	}

	const steps: Step[] = [
		{
			number: 1,
			title: 'Install',
			tabs: [
				{ label: 'Laravel', lang: 'bash', code: 'composer require lettr/lettr-php' },
				{ label: 'Node.js', lang: 'bash', code: 'npm install @lettr/node' },
				{ label: 'Python', lang: 'bash', code: 'pip install lettr' },
				{ label: 'Go', lang: 'bash', code: 'go get github.com/lettr/lettr-go' },
				{ label: 'Ruby', lang: 'bash', code: 'gem install lettr' },
				{ label: 'cURL', lang: 'bash', code: '# No installation needed' }
			]
		},
		{
			number: 2,
			title: 'Initialize the client',
			tabs: [
				{
					label: 'Laravel',
					lang: 'php',
					code: `use Lettr\\Lettr;

$lettr = Lettr::client($apiKey);`
				},
				{
					label: 'Node.js',
					lang: 'javascript',
					code: `import { Lettr } from '@lettr/node';

const lettr = new Lettr(process.env.LETTR_API_KEY);`
				},
				{
					label: 'Python',
					lang: 'python',
					code: `import lettr

client = lettr.Client(api_key="your_api_key")`
				},
				{
					label: 'Go',
					lang: 'go',
					code: `import "github.com/lettr/lettr-go"

client := lettr.NewClient("your_api_key")`
				},
				{
					label: 'Ruby',
					lang: 'ruby',
					code: `require 'lettr'

client = Lettr::Client.new(api_key: 'your_api_key')`
				},
				{
					label: 'cURL',
					lang: 'bash',
					code: `# Set your API key
export LETTR_API_KEY="your_api_key"`
				}
			]
		},
		{
			number: 3,
			title: 'Build your email',
			tabs: [
				{
					label: 'Laravel',
					lang: 'php',
					code: `$email = $lettr->emails()->create()
    ->from('hello@yourapp.com', 'Your App')
    ->to([$user->email])
    ->subject('Welcome!')
    ->html($html);`
				},
				{
					label: 'Node.js',
					lang: 'javascript',
					code: `const email = lettr.emails.create({
  from: 'hello@yourapp.com',
  to: user.email,
  subject: 'Welcome!',
  html: html,
});`
				},
				{
					label: 'Python',
					lang: 'python',
					code: `email = client.emails.create(
    from_email="hello@yourapp.com",
    to=user.email,
    subject="Welcome!",
    html=html,
)`
				},
				{
					label: 'Go',
					lang: 'go',
					code: `email := client.Emails.Create(&lettr.EmailParams{
    From:    "hello@yourapp.com",
    To:      user.Email,
    Subject: "Welcome!",
    HTML:    html,
})`
				},
				{
					label: 'Ruby',
					lang: 'ruby',
					code: `email = client.emails.create(
  from: 'hello@yourapp.com',
  to: user.email,
  subject: 'Welcome!',
  html: html
)`
				},
				{
					label: 'cURL',
					lang: 'bash',
					code: `# Emails are built inline with the send request`
				}
			]
		},
		{
			number: 4,
			title: 'Send it',
			tabs: [
				{
					label: 'Laravel',
					lang: 'php',
					code: `$response = $lettr->emails()->send($email);

echo $response->requestId;`
				},
				{
					label: 'Node.js',
					lang: 'javascript',
					code: `const response = await lettr.emails.send(email);

console.log(response.requestId);`
				},
				{
					label: 'Python',
					lang: 'python',
					code: `response = client.emails.send(email)

print(response.request_id)`
				},
				{
					label: 'Go',
					lang: 'go',
					code: `response, err := client.Emails.Send(email)

fmt.Println(response.RequestID)`
				},
				{
					label: 'Ruby',
					lang: 'ruby',
					code: `response = client.emails.send(email)

puts response.request_id`
				},
				{
					label: 'cURL',
					lang: 'bash',
					code: `curl -X POST https://api.lettr.dev/v1/send \\
  -H "Authorization: Bearer $LETTR_API_KEY" \\
  -H "Content-Type: application/json" \\
  -d '{
    "from": "hello@yourapp.com",
    "to": "user@example.com",
    "subject": "Welcome!",
    "html": "<h1>Hello!</h1>"
  }'`
				}
			]
		}
	];

	const primaryTabIndices = [0, 1];
	const moreTabIndices = [2, 3, 4, 5];

	onMount(() => {
		if (!section) return;

		gsap.from(section.querySelectorAll('[data-step]'), {
			scrollTrigger: {
				trigger: section,
				start: 'top 80%',
				toggleActions: 'play none none none'
			},
			y: 30,
			opacity: 0,
			duration: 0.6,
			stagger: 0.12,
			ease: 'power3.out'
		});
	});
</script>

<section bind:this={section} class="px-4 py-16 border-b border-border/30">
	<div class="mx-auto max-w-[550px]">
		<div class="mb-12 text-center" data-step>
			<h2 class="mb-3 text-surface">Get started <span class="text-primary">in minutes</span></h2>
			<p class="text-body text-muted">
				Four steps to sending your first email. No complicated setup.
			</p>
		</div>

		<div class="flex flex-col gap-10">
			{#each steps as step}
				<div data-step>
					<h3 class="mb-3 text-surface">
						<span class="text-primary">{step.number}.</span> {step.title}
					</h3>

					<CodeSnippet tabs={step.tabs} {primaryTabIndices} {moreTabIndices} shadow={false} />
				</div>
			{/each}
		</div>
	</div>
</section>
