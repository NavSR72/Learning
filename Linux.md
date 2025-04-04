Linux File Types
   - regular file, directory, special file
   - regular file contains images, scripts, configuration, data files
   - directory ex: /home/bob
   - special files divided into five types
   - character files used for IO communication ex: mouse 
   - block files used to represent the block devices ex: harddisk, RAM
   - links divided into two hard links and soft links 
   - hard links  associates two or more file share the same data on the physical disk. They behave as independent files
   - soft links pointers to other files
   - sockets - *Socket file* in Unix is used for IPC(Inter Process Communication)
           A Unix Domain Socket is a special file that allows bidirectional communication between processes, either on the same machine or across networks (with TCP/UDP sockets).
    	    - Works like a network connection, but is much faster if used locally.
    	    - Used heavily by system daemons (e.g., docker.sock, X11, Wayland, databases).
          - Supports multiple clients (unlike named pipes).
            
          Pros & Cons of using TCP & Socket in IPC
     
          ### TCP
          - Communicates through IP Address & TCP Port
          - May runs out of TCP Port
          - May accidentally expose the connection to public network
          
          ### Unix Domain Socket
          - Communicates through socket file
          - Can create infinite socket file
          - Only accessible from the same machine
          
          To find socket files in your system
          
          ```find / -type s```
          
          Output will be like
     
          ```
          # Unix Socket Users
          Docker (HTTP-over-UDS)
          HAProxy
          kitty
          mpv
          xil
          ```

    # Named Pipes
    
      - One-time or short-term communication between two processes
      - Inter-process data exchange in shell scripts
      - Data flow between applications or commands

      # Linux File Types

Linux supports various file types beyond just regular files and directories. Below is a categorized breakdown:

## 1. Regular Files
- Contain images, scripts, configuration files, data files, etc.

## 2. Directories
- Example: `/home/bob`
- Store other files and directories.

## 3. Special Files

Special files are used for hardware access and advanced features. They are divided into:

### Character Files
- Used for I/O communication.
- Example: Mouse, keyboard, serial ports.

### Block Files
- Represent block devices like hard disks, SSDs, and RAM.

### Links
Links are used to reference other files.

#### Hard Links
- Associate two or more files that share the same data on the physical disk.
- Behave as independent files.

#### Soft Links (Symbolic Links)
- Act as pointers to other files.
- Break if the original file is deleted.

### Sockets
- Used for **Inter-Process Communication (IPC)**.
- A **Unix Domain Socket** is a special file that allows bidirectional communication between processes on the same system or over a network (when using TCP/UDP sockets).

#### Characteristics
- Acts like a network connection, but faster for local use.
- Used heavily by system daemons (e.g., `docker.sock`, `X11`, Wayland, databases).
- Supports multiple clients (unlike named pipes).

#### Pros & Cons of IPC Methods

##### TCP
- Communicates via IP Address and TCP Port.
- May run out of available TCP ports.
- Risk of accidentally exposing connection to public networks.

##### Unix Domain Sockets
- Communicate via a socket file on the filesystem.
- Can create as many socket files as needed.
- Only accessible from the same machine (more secure for local communication).

#### Finding Socket Files
Use the following command:

```bash
find / -type s
