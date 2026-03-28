# ---------------- DEADLOCK DETECTION USING RAG ----------------

# Graph representation (Resource Allocation Graph)
graph = {
    "P1": ["R1"],
    "R1": ["P2"],
    "P2": ["R2"],
    "R2": ["P1"]
}

visited = set()
rec_stack = set()

def detect_cycle(node):
    visited.add(node)
    rec_stack.add(node)

    for neighbor in graph.get(node, []):
        if neighbor not in visited:
            if detect_cycle(neighbor):
                return True
        elif neighbor in rec_stack:
            return True

    rec_stack.remove(node)
    return False

def is_deadlock():
    for node in graph:
        if node not in visited:
            if detect_cycle(node):
                return True
    return False


# ---------------- DEADLOCK CREATION USING SEMAPHORES ----------------

import threading
import time

# Two resources (semaphores)
R1 = threading.Semaphore(1)
R2 = threading.Semaphore(1)

def process1():
    print("P1 waiting for R1")
    R1.acquire()
    print("P1 acquired R1")

    time.sleep(1)

    print("P1 waiting for R2")
    R2.acquire()
    print("P1 acquired R2")

    R2.release()
    R1.release()

def process2():
    print("P2 waiting for R2")
    R2.acquire()
    print("P2 acquired R2")

    time.sleep(1)

    print("P2 waiting for R1")
    R1.acquire()
    print("P2 acquired R1")

    R1.release()
    R2.release()


# ---------------- MAIN EXECUTION ----------------

if __name__ == "__main__":
    # Create threads (process simulation)
    t1 = threading.Thread(target=process1)
    t2 = threading.Thread(target=process2)

    t1.start()
    t2.start()

    # Wait for threads (may hang due to deadlock)
    t1.join(timeout=3)
    t2.join(timeout=3)

    print("\nChecking for Deadlock using Resource Allocation Graph...")

    if is_deadlock():
        print("Deadlock Detected!")
    else:
        print("No Deadlock")
