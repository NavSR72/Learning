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
          
          `find / -type s`
          
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
