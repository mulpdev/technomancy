https://stackoverflow.com/questions/35821184/implement-an-interactive-shell-over-ssh-in-python-using-paramiko
```python
import paramiko
import sys
import logging
#logging.basicConfig(level=logging.DEBUG) # for paramiko debug

HOST = sys.argv[1]
USER = "user"
PASS = "pass"

client = paramiko.client.SSHClient()
client.set_missing_host_key_policy(paramiko.AutoAddPolicy)

def press_for_more_output(data, prompt):
	return prompt in data
	# may also return last count/index of prompt in data

def get_prompt_index(data, prompt):
	return prompt in data
	
	# if the cumulative buffer ends in a prompt
	# it's probably near the very end of the string
	# and might only be succeeded by white space (0x20, 0x0a, 0x0d)
	# or an escape sequence (0x1b ...)


def cleanup_output(stdout_buffer):
	ESCSEQ = "0x"
	if ESCSEQ in cmd1_out:
		print("Removing escape sequences")
		cmd1_out = cmd1_out.replace(escseq, '')
	
	PRESS_TO_CONTINUE = "press to continue"
	if PRESS_TO_CONTINUE in cmd1_out:
		print("Removing press to contiue prompt")
		cmd1_out = cmd1_out.replace(escseq, '')

	lines = stdout_buffer.split('\n')

	# ditch echoed commands here, e.g.,
	# <prompt> cmd1
	for line in lines:
		x = get_prompt_index(lines, "prompt")
		y = line[x:]
		y = y.strip()
		if len(y) > 0:
			cmd1_out.replace(line, '')

def send_command(cmd):
	channel.send(cmd1)
	stdout_buffer = ""
	while True:
		if channel.exit_status_ready():
			print("exit_status_rady is True")
			break
		if channel.recv_ready():
			tmp = channel.recv(100).decode('utf-8')
			stdout_buffer += tmp

			# process tmp here
			# WARN: tmp MAY have cut off commands! Code around that!

			# Does command need input to continue?
			press_to_continue = press_for_more_output(stdout_buffer, prompt)
			if press_to_continue:
				key = '\n'
				channel.send(key)

			# process cumulative stdout_buffer here

			# look for next prompt in cumulative output
			pi = get_prompt_index(stdout_buffer, "<prompt>")

			# is it some arbitrary number of chars away from end?
			if len(stdout_buffer[pi:]) < 10:
				print("Next prompt detected")
				break

	return stdout_buffer

def main():
	cmd1 = "foo"
	cmd2 = "bar"

	client.connect(HOST, username=USER, password=PASS, look_for_keys=False)
	# Channel is ALL of stdin/stdout/stderr
	# does not need to be actual bash/sh/pty to call invoke_shell()
	channel = client.invoke_shell() 

	# first command
	cmd1_out = send_command(cmd1)
	cmd1_out_clean = cleanup_output(cmd1_out)
	
	# subsequent commands
	cmdX_out = send_command(cmdX)
	cmdX_out_clean = cleanup_output(cmdX_out)

	client.close()


if __name__ = "__main__":
	main()

```