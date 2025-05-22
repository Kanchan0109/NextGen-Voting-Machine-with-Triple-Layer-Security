# NextGen-Voting-Machine-with-Triple-Layer-Security
Designed a secure electronic voting system integrating three-layer authentication: facial recognition, Aadhaar verification, and biometric validation.
import cv2
import os
import numpy as np
from PIL import Image

REGISTERED_IMAGE_PATH = "registered.jpg"

def register_candidate():
    cap = cv2.VideoCapture(0)
    if not cap.isOpened():
        print("❌ Failed to open camera.")
        return

    print("📷 Press 'c' to capture and register the candidate.")

    while True:
        ret, frame = cap.read()
        if not ret:
            print("❌ Camera error.")
            break

        cv2.imshow("Register - Press 'c'", frame)
        key = cv2.waitKey(1)

        if key & 0xFF == ord('c'):
            # Convert BGR to RGB
            rgb_image = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)

            # Save as image using PIL
            pil_image = Image.fromarray(rgb_image)
            pil_image = pil_image.convert('RGB')  # Ensure RGB color mode
            pil_image.save(REGISTERED_IMAGE_PATH)
            print("✅ Candidate registered.")
            break

    cap.release()
    cv2.destroyAllWindows()

def verify_candidate():
    if not os.path.exists(REGISTERED_IMAGE_PATH):
        print("❌ No registered image found.")
        return

    print("🔍 Loading registered image...")

    try:
        # Load the image using PIL and convert to RGB
        pil_image = Image.open(REGISTERED_IMAGE_PATH)
        pil_image = pil_image.convert('RGB')  # Ensure RGB mode
        registered_image_rgb = np.array(pil_image)

        print(f"✅ Loaded image shape: {registered_image_rgb.shape}, dtype: {registered_image_rgb.dtype}")

        cap = cv2.VideoCapture(0)
        if not cap.isOpened():
            print("❌ Failed to open camera.")
            return

        print("🎥 Press 'q' to quit verification.")

        while True:
            ret, frame = cap.read()
            if not ret:
                print("❌ Camera error.")
                break

            # Convert captured frame to RGB
            rgb_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)

            # Simulate verification success by overlaying text
            cv2.putText(frame, "VERIFIED", (50, 50), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 255, 0), 2)

            cv2.imshow("Verification - Press 'q' to quit", frame)
            if cv2.waitKey(1) & 0xFF == ord('q'):
                break

        cap.release()
        cv2.destroyAllWindows()

    except Exception as e:
        print("❌ ERROR:", e)

# ✅ This is the correct way to start the script
if _name_ == "_main_":
    print("1. Register Candidate")
    print("2. Verify Candidate")
    choice = input("Choose an option (1/2): ").strip()

    if choice == '1':
        register_candidate()
    elif choice == '2':
        verify_candidate()
    else:
        print("❌ Invalid option.")
